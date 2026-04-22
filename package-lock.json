/**
 * Rule-based Mock AI logic for predicting careers based on skills and interests.
 * In a real-world application, this would load an ML model (.tflite, ONNX, or Python microservice).
 */

const CAREERS_DATABASE = [
    {
        id: "C001",
        title: "Software Engineer",
        description: "Design, develop, and test software systems and applications.",
        requiredSkills: ["java", "python", "javascript", "c++", "problem solving", "algorithms", "data structures"],
        requiredInterests: ["coding", "technology", "logic", "building things"],
        minMarks: 70
    },
    {
        id: "C002",
        title: "Data Scientist",
        description: "Analyze and interpret complex data to help organizations make better decisions.",
        requiredSkills: ["machine learning", "python", "r", "statistics", "sql", "data visualization", "pandas"],
        requiredInterests: ["math", "data", "research", "patterns"],
        minMarks: 75
    },
    {
        id: "C003",
        title: "UX/UI Designer",
        description: "Create user-friendly interfaces and experiences for digital products.",
        requiredSkills: ["figma", "wireframing", "prototyping", "adobe xd", "css", "creativity"],
        requiredInterests: ["design", "art", "psychology", "user interaction"],
        minMarks: 60
    },
    {
        id: "C004",
        title: "Cybersecurity Analyst",
        description: "Protect computer networks and systems from cyber threats.",
        requiredSkills: ["networking", "linux", "ethical hacking", "cryptography", "security tools", "python"],
        requiredInterests: ["security", "investigation", "networks", "defense"],
        minMarks: 65
    },
    {
        id: "C005",
        title: "Marketing Manager",
        description: "Plan and execute strategies to promote brands, products, and services.",
        requiredSkills: ["seo", "content writing", "social media", "communication", "analytics", "campaign management"],
        requiredInterests: ["business", "communication", "psychology", "strategy", "writing"],
        minMarks: 60
    },
    {
        id: "C006",
        title: "Cloud Architect",
        description: "Design and manage cloud computing architecture and deployment strategies.",
        requiredSkills: ["aws", "azure", "docker", "kubernetes", "linux", "networking", "terraform"],
        requiredInterests: ["infrastructure", "servers", "automation", "scale"],
        minMarks: 70
    }
];

function predictCareers(userData) {
    const { skills = [], interests = [], marks = 0, preferredSubjects = [] } = userData;
    
    // Normalize user inputs
    const userSkills = skills.map(s => s.toLowerCase());
    const userInterests = interests.map(i => i.toLowerCase());
    
    // Score careers based on matches
    const scoredCareers = CAREERS_DATABASE.map(career => {
        let score = 0;
        
        // Match skills (weight: 2)
        const commonSkills = career.requiredSkills.filter(r => userSkills.includes(r.toLowerCase()));
        score += commonSkills.length * 2;
        
        // Match interests (weight: 1.5)
        const commonInterests = career.requiredInterests.filter(i => userInterests.includes(i.toLowerCase()));
        score += commonInterests.length * 1.5;
        
        // Filter out if marks are too low (penalty)
        if (marks < career.minMarks) {
            score -= 2;
        } else {
            score += 1;
        }

        return {
            ...career,
            matchScore: score,
            matchedSkills: commonSkills,
            matchedInterests: commonInterests
        };
    });
    
    // Sort by score descending
    scoredCareers.sort((a, b) => b.matchScore - a.matchScore);
    
    // Pick the top 3 with positive match scores (or at least the top ones)
    return scoredCareers.slice(0, 3).map(c => ({
        title: c.title,
        description: c.description,
        matchPercentage: Math.min(100, Math.round((c.matchScore / 10) * 100)) + '%', // Pseudo percentage
        tags: c.requiredSkills.slice(0, 3)
    }));
}

module.exports = {
    predictCareers
};
