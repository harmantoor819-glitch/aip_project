const express = require('express');
const cors = require('cors');
const bodyParser = require('body-parser');
const serverless = require('serverless-http');
const { MongoClient } = require('mongodb');
const recommender = require('./recommender');

const app = express();
app.use(cors());
app.use(bodyParser.json());

// MongoDB connection
const MONGO_URL = process.env.MONGO_URL || 'mongodb://127.0.0.1:27017';
let cachedDb = null;

async function getDb() {
    if (cachedDb) {
        return cachedDb;
    }
    try {
        const client = await MongoClient.connect(MONGO_URL);
        cachedDb = client.db('smart_career_guidance');
        console.log('✅ Connected to MongoDB');
        return cachedDb;
    } catch (err) {
        console.error('❌ Failed to connect to MongoDB:', err);
        throw err;
    }
}

// Use a router to prefix all routes with /api since netlify redirect maps to /api
const router = express.Router();

// Auth endpoints
router.post('/auth/signup', async (req, res) => {
    try {
        const db = await getDb();
        const { name, email, password } = req.body;
        const users = db.collection('users');
        const existing = await users.findOne({ email });
        if (existing) {
            return res.status(400).json({ success: false, message: 'User already exists' });
        }
        const newUser = { name, email, password, role: 'user' };
        await users.insertOne(newUser);
        res.status(201).json({ success: true, user: { name, email, role: 'user' } });
    } catch (error) {
        console.error(error);
        res.status(500).json({ success: false, message: 'Server error' });
    }
});

router.post('/auth/login', async (req, res) => {
    try {
        const db = await getDb();
        const { email, password } = req.body;
        const users = db.collection('users');
        const user = await users.findOne({ email, password });
        if (user) {
            res.status(200).json({ success: true, user: { name: user.name, email: user.email, role: user.role } });
        } else {
            res.status(401).json({ success: false, message: 'Invalid credentials' });
        }
    } catch (error) {
        console.error(error);
        res.status(500).json({ success: false, message: 'Server error' });
    }
});

// Predictions endpoints
router.post('/predictions', async (req, res) => {
    try {
        const db = await getDb();
        const predictionData = req.body;
        predictionData.timestamp = new Date();
        const predictions = db.collection('predictions');
        await predictions.insertOne(predictionData);
        res.status(201).json({ success: true });
    } catch (error) {
        console.error(error);
        res.status(500).json({ success: false, message: 'Failed to save prediction' });
    }
});

router.get('/predictions/:email', async (req, res) => {
    try {
        const db = await getDb();
        const email = req.params.email;
        const predictions = db.collection('predictions');
        const history = await predictions.find({ userEmail: email }).sort({ timestamp: -1 }).toArray();
        res.status(200).json({ success: true, history });
    } catch (error) {
        console.error(error);
        res.status(500).json({ success: false, message: 'Server error' });
    }
});

// Main recommendation endpoint
router.post('/recommend', (req, res) => {
    try {
        const userData = req.body;
        const recommendations = recommender.predictCareers(userData);
        res.status(200).json({ success: true, careers: recommendations });
    } catch (error) {
        console.error('Prediction Error:', error);
        res.status(500).json({ success: false, message: 'Internal Server Error' });
    }
});

// Admin stats endpoint
router.get('/admin/stats', async (req, res) => {
    try {
        const db = await getDb();
        const totalUsers = await db.collection('users').countDocuments();
        const totalPredictions = await db.collection('predictions').countDocuments();
        res.status(200).json({ success: true, stats: { totalUsers, totalPredictions } });
    } catch (error) {
        console.error(error);
        res.status(500).json({ success: false, message: 'Failed to fetch stats' });
    }
});

// Health check endpoint
router.get('/health', (req, res) => {
    res.status(200).json({ status: 'OK' });
});

app.use('/api', router); // Local test config
app.use('/.netlify/functions/api', router); // Netlify Serverless Cloud config

// We don't listen on a port here. We export wrapped express app.
module.exports.handler = serverless(app);
