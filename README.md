============================================================================
                          SMOz System Technical Documentation
============================================================================

System Version: v1.0
Last Updated: 2025-01-27
Status: Production Ready

============================================================================
                              Table of Contents
============================================================================

1. System Overview
2. Architecture
3. Core Components
4. Memory System
5. API Endpoints
6. Requirements
7. Deployment
8. Development Guide

============================================================================
                             1. System Overview
============================================================================

SMOz is a multi-layered AI conversation system that simulates human-like memory 
and interaction patterns. The system consists of three main services:

- Orchestration Server (Node.js/TypeScript) - Manages avatar interactions and session handling
- OZ Server (Python/FastAPI) - Core AI processing with advanced memory management
- Avatar Web Interface (React) - User-facing web application

Key Features:
- Three-Tier Memory System: Short-term, medium-term, and long-term memory layers
- Multi-User Support: Isolated user sessions with individual memory systems
- Real-time Communication: WebSocket-based real-time messaging
- Intelligent Context Management: AI-driven conversation context and memory optimization
- Avatar Integration: Support for multiple AI personas and avatars

============================================================================
                               2. Architecture
============================================================================

┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Avatar Web    │    │ Orchestration   │    │   OZ Server     │
│   (React)       │◄──►│   Server        │◄──►│   (Python)      │
│   Port: 3000    │    │   (Node.js)     │    │   Port: 3002    │
│                 │    │   Port: 3001    │    │                 │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │                       │                       │
         └───────────────────────┼───────────────────────┘
                                 │
                    ┌─────────────────┐
                    │   Memory        │
                    │   Storage       │
                    │   (Local JSON)  │
                    └─────────────────┘

Data Flow:
1. User interacts with Avatar Web interface
2. WebSocket connection established with Orchestration Server
3. Orchestration Server forwards messages to OZ Server via HTTP
4. OZ Server processes with AI models and memory system
5. Response returned through the same path
6. Real-time updates sent via WebSocket

============================================================================
                             3. Core Components
============================================================================

3.1 Orchestration Server (sm-orchestration/)

Technology Stack:
- Node.js with TypeScript
- Express.js for HTTP server
- WebSocket for real-time communication
- Avatar management system

Key Responsibilities:
- Session management and user authentication
- Avatar persona handling
- WebSocket connection management
- Message routing between components
- Inactivity detection and timeout handling

Main Files:
- src/server.ts - Main server implementation
- src/avatar.ts - Avatar base class
- src/avatarFactory.ts - Avatar creation factory
- src/engine*.ts - Various AI engine integrations

3.2 OZ Server (oz-server/)

Technology Stack:
- Python 3.8+
- FastAPI for REST API
- LangChain for AI processing
- OpenAI/Groq for LLM integration
- WebSocket support

Key Responsibilities:
- AI conversation processing
- Memory system management
- User profile management
- Content moderation and sensitivity detection
- Multi-user session handling

Main Files:
- src/app.py - FastAPI application and endpoints
- src/chat.py - Core chat processing logic
- src/memory_system_enhanced.py - Advanced memory system
- src/memory_system_v2.py - Memory system version 2
- src/time_parser.py - Time-related utilities

3.3 Avatar Web Interface (sm-avatar-web/)

Technology Stack:
- React 17
- Redux for state management
- Soul Machines Web SDK
- Bootstrap for UI components

Key Responsibilities:
- User interface for avatar interactions
- Real-time chat display
- Avatar visualization
- Session management UI

============================================================================
                              4. Memory System
============================================================================

The SMOz memory system is one of its most sophisticated features, implementing 
a three-tier architecture that mimics human memory patterns.

4.1 Memory Layers

4.1.1 Short-Term Memory (STM)
- Purpose: Stores recent dialogue (5-10 turns) for session continuity
- Storage: short_term/session_YYYYMMDD_HHMMSS.json
- Content: Recent conversation turns with metadata
- Lifecycle: Auto-cleared periodically

4.1.2 Medium-Term Memory (MTM)
- Purpose: Stores emotionally or personally significant conversations
- Storage: medium_term/important_memories.json
- Content: Important conversations with emotional context
- Trigger: Importance score > 0.5, emotional intensity, personal information

4.1.3 Long-Term Memory (LTM)
- Purpose: Stores structured user profiles (no raw dialogue)
- Storage: long_term/insights_and_summaries.json
- Content: User preferences, behavior traits, statistical summaries
- Privacy: Contains NO raw dialogue content

4.2 Memory Management Features

- Automatic Cleanup: Periodic memory optimization and compression
- Importance Scoring: AI-driven conversation importance assessment (0.0-1.0)
- Smart Compression: Intelligent summarization of conversations
- Privacy Protection: No long-term storage of raw dialogue
- Multi-User Isolation: Separate memory systems per user

4.3 File Structure

sm-docker-local/data/memory/default_user/
├── metadata/
│   └── user_metadata.json
├── short_term/
│   └── session_YYYYMMDD_HHMMSS.json
├── medium_term/
│   └── important_memories.json
├── long_term/
│   └── insights_and_summaries.json
└── backup_YYYYMMDD_HHMMSS/

4.4 Memory Content Examples

4.4.1 Long-Term Memory Structure
{
  "user_profile": {
    "user_id": "default_user",
    "companion_start_date": "2025-05-30",
    "topics_of_interest": ["Greeting", "Friday updates"],
    "emotional_tendencies": ["Friendly", "Positive"],
    "communication_style": "Reserved communicator",
    "relationship_stage": "Initial contact",
    "activity_level": "Normal interaction",
    "information_sharing_tendency": "Low",
    "average_importance": 0.32,
    "interaction_summary": {
      "total_conversations": 1,
      "last_interaction": "2025-05-30T15:00:13.879556"
    },
    "preferences": {},
    "behavior_traits": ["Concise communication", "Polite interaction"],
    "summary_stats": {
      "most_discussed_topics": {"Greeting": 1, "Friday updates": 1},
      "dominant_emotions": {"Friendly": 1, "Positive": 1},
      "relationship_duration_days": 0
    }
  }
}

4.4.2 Medium-Term Memory Structure
[
  {
    "timestamp": "2025-05-30T16:30:15",
    "importance": 0.85,
    "conversation": [
      {
        "user": "I've been feeling off today — work has been really stressful.",
        "ai": "I totally understand. Work pressure can really affect your mood."
      }
    ],
    "topics": ["Work", "Emotional Support"],
    "emotions": ["Anxiety", "Stress"],
    "insight": "The user opened up for the first time about work-related emotional distress."
  }
]

4.4.3 Short-Term Memory Structure
[
  {
    "timestamp": "2025-05-30T15:00:10",
    "user": "hi, how are",
    "ai": "Hi there! I'm doing great, thanks for asking. ��",
    "topics": ["Greeting", "Friday updates"],
    "emotions": ["Friendly", "Positive"],
    "importance": 0.32
  }
]

============================================================================
                              5. API Endpoints
============================================================================

5.1 OZ Server Endpoints

5.1.1 Chat Processing
POST /generate
- Process user messages and generate AI responses
- Request: {"query": "user message", "user_id": "user_id"}
- Response: {"ai_proposed_message": "AI response", "user_id": "user_id"}

GET /history
- Retrieve chat history
- Response: Array of chat messages

GET /feature-flags
- Get current feature configuration
- Response: Feature flags configuration

POST /feature-flags
- Update feature configuration
- Request: Feature flags object

5.1.2 Memory Management
GET /memory-status
- Get overall memory system status
- Response: Memory system statistics and status

GET /memory-status/{user_id}
- Get specific user's memory status
- Response: User-specific memory information

GET /users
- List all active users
- Response: Array of user IDs

POST /memory-save
- Save current memory state
- Response: Success status

POST /memory-save/{user_id}
- Save specific user's memory
- Response: Success status

POST /memory-clear/{user_id}
- Clear user's memory
- Response: Success status

POST /memory-switch
- Switch between memory systems
- Request: {"use_enhanced": true/false}
- Response: Success status

5.1.3 WebSocket
WS /ws
- Real-time communication endpoint
- Handles bidirectional communication

5.2 Orchestration Server Endpoints

GET /healthz
- Health check
- Response: {"status": "ok"}

GET /healthz/version
- Version information
- Response: {"version": "version_number"}

POST /event
- Event processing
- Request: Event object
- Response: Success status

WS /
- WebSocket connection for real-time communication

============================================================================
                               6. Requirements
============================================================================

6.1 System Requirements

Minimum Requirements:
- CPU: 2 cores
- RAM: 4GB
- Storage: 10GB available space
- Network: Stable internet connection for API calls

Recommended Requirements:
- CPU: 4+ cores
- RAM: 8GB+
- Storage: 20GB+ available space
- Network: High-speed internet connection

6.2 Software Requirements

Development Environment:
- Docker: 20.10+
- Docker Compose: 2.0+
- Node.js: 16+ (for orchestration server)
- Python: 3.8+ (for OZ server)
- Git: Latest version

6.3 Runtime Dependencies

OZ Server (Python):
FastAPI
uvicorn[standard]
langchain
langchain-openai
openai
websocket
groq
langchain-community
python-dotenv
pydantic
pytz==2023.3
requests==2.31.0

Orchestration Server (Node.js):
express: ^4.17.1
ws: ^7.4.3
axios: ^1.5.0
openai: ^4.20.0
luxon: ^3.4.4
uuid: ^9.0.0

Avatar Web (React):
react: ^17.0.2
@soulmachines/smwebsdk: 15.7.0
redux: ^4.1.0
react-router-dom: ^5.2.0

6.4 API Keys Required

- OpenAI API Key: For LLM processing and embeddings
- Groq API Key: Alternative LLM provider
- SSL Certificates: For HTTPS in development (optional)

6.5 Environment Variables

Required:
OPENAI_API_KEY=your_openai_api_key
GROQ_API_KEY=your_groq_api_key

Optional:
NODE_ENV=development
TZ=Asia/Shanghai
APP_TIMEZONE=Asia/Shanghai
TIME_SYNC_ENABLED=true
NTP_SERVER=pool.ntp.org

SSL (for development):
SSL_KEY=path_to_private_key
SSL_CERT=path_to_certificate

============================================================================
                               7. Deployment
============================================================================

7.1 Docker Deployment

The system is designed for Docker deployment using the provided docker-compose.yml:

version: '3.8'

services:
  orchestration-server:
    build: ./sm-orchestration
    ports:
      - "3001:3001"
    environment:
      - NODE_ENV=development
    volumes:
      - ./sm-orchestration:/app
      - /app/node_modules

  oz-server:
    build: ./oz-server
    ports:
      - "3002:3002"
    environment:
      - PYTHONPATH=/code/src
      - OPENAI_API_KEY=${OPENAI_API_KEY}
      - GROQ_API_KEY=${GROQ_API_KEY}
      - TZ=Asia/Shanghai
      - APP_TIMEZONE=Asia/Shanghai
      - TIME_SYNC_ENABLED=true
      - NTP_SERVER=pool.ntp.org
    volumes:
      - ./oz-server:/code
      - ./sm-docker-local:/code/sm-docker-local

7.2 Deployment Steps

1. Clone Repository
   git clone <repository-url>
   cd SMOz-Langchain

2. Set Environment Variables
   export OPENAI_API_KEY="your_openai_api_key"
   export GROQ_API_KEY="your_groq_api_key"

3. Build and Start Services
   docker-compose up --build

4. Access Services
   - Orchestration Server: http://localhost:3001
   - OZ Server: http://localhost:3002
   - Avatar Web: http://localhost:3000

7.3 Production Considerations

- SSL/TLS: Configure proper SSL certificates
- Load Balancing: Use reverse proxy (nginx/traefik)
- Database: Consider persistent database for production
- Monitoring: Implement logging and monitoring
- Backup: Regular backup of memory data
- Security: Implement proper authentication and authorization

============================================================================
                             8. Development Guide
============================================================================

8.1 Local Development Setup

1. Install Dependencies

OZ Server:
cd oz-server
pip install -r requirements.txt

Orchestration Server:
cd ../sm-orchestration
npm install

Avatar Web:
cd ../sm-avatar-web
npm install

2. Start Development Servers

OZ Server:
cd oz-server
python -m uvicorn src.app:app --reload --port 3002

Orchestration Server:
cd ../sm-orchestration
npm run dev

Avatar Web:
cd ../sm-avatar-web
npm run dev

8.2 Testing

Memory System Testing:
cd oz-server
python test_memory_optimization.py
python test_memory_v2.py

API Testing:
# Test OZ Server endpoints
curl -X POST http://localhost:3002/generate \
  -H "Content-Type: application/json" \
  -d '{"query": "Hello", "user_id": "test_user"}'

# Test memory status
curl http://localhost:3002/memory-status

8.3 Code Structure

Key Development Files:
- oz-server/src/memory_system_enhanced.py - Core memory system
- oz-server/src/chat.py - Chat processing logic
- sm-orchestration/src/server.ts - Main orchestration server
- sm-avatar-web/src/ - React application components

Configuration Files:
- docker-compose.yml - Service orchestration
- oz-server/requirements.txt - Python dependencies
- sm-orchestration/package.json - Node.js dependencies
- sm-avatar-web/package.json - React dependencies

8.4 Best Practices

1. Memory Management
   - Regular memory optimization
   - Monitor memory usage
   - Implement proper cleanup

2. Error Handling
   - Comprehensive error logging
   - Graceful degradation
   - User-friendly error messages

3. Performance
   - Optimize API response times
   - Implement caching where appropriate
   - Monitor resource usage

4. Security
   - Validate all inputs
   - Implement rate limiting
   - Secure API key management

8.5 Troubleshooting

Common Issues:

1. Memory System Not Loading
   - Check file permissions
   - Verify JSON file integrity
   - Check API key configuration

2. WebSocket Connection Issues
   - Verify port configurations
   - Check firewall settings
   - Ensure proper SSL setup

3. AI Response Issues
   - Verify API key validity
   - Check network connectivity
   - Monitor API rate limits

Debug Commands:
# Check service status
docker-compose ps

# View logs
docker-compose logs oz-server
docker-compose logs orchestration-server

# Memory system status
curl http://localhost:3002/memory-status

============================================================================
                              Memory Management
============================================================================

Automatic Cleanup Cycles:
- 15 conversations: Clear low-importance STM entries
- 20 conversations: Compress MTM, extract profile updates
- 25 conversations: Generate behavioral insights
- 30 conversations: Update comprehensive user profile

Importance Scoring System:
- 0.0-0.3: Daily greetings, general chat
- 0.4-0.6: Moderately valuable dialogue
- 0.7-0.8: Important personal sharing
- 0.9-1.0: Critical profile information and deep insights

Storage Rotation:
- STM: 5-10 recent turns, auto-pruned
- MTM: Important conversations only, selective retention
- LTM: Structured profile data, continuously updated

============================================================================
                           Technical Implementation
============================================================================

Core Components:
- EnhancedMemorySystem: Main memory management class
- LangChain Integration: Document processing and vector storage
- OpenAI Embeddings: Text vectorization for semantic search
- Automatic Compression: Intelligent memory compression and summarization

Key Files:
- oz-server/src/memory_system_enhanced.py: Core memory system
- memory_optimization_script.py: Memory reorganization script
- test_memory_optimization.py: Comprehensive testing suite

Storage Technology:
- JSON format for human-readable storage
- Vector database for semantic search
- Hierarchical file structure for easy management
- Automatic backup system with versioning

============================================================================
                              Usage Guide
============================================================================

System Initialization:
from memory_system_enhanced import EnhancedMemorySystem
memory = EnhancedMemorySystem(user_id="your_user_id")

Adding Conversations:
memory.add_conversation(user_message, ai_response)

Retrieving Memories:
# Get structured user profile
user_profile = memory.get_user_profile()

# Search relevant memories
relevant_memories = memory.search_memories(query, memory_types=["long_term"])

# Get important conversations
important_convs = memory.get_important_conversations()

Manual Operations:
# Trigger memory optimization
memory.compress_short_term_memory()

# Backup current state
memory.create_backup()

# Update user profile
memory.update_user_profile(new_traits)

============================================================================
                           Privacy & Security
============================================================================

Privacy Protection:
- Long-term memory contains NO dialogue content
- Only behavioral patterns and preferences stored
- Automatic cleanup of sensitive short-term data
- User control over data retention periods

Data Security:
- Local storage only (no cloud dependency)
- Encrypted backups available
- User data isolation by user_id
- Audit trail for all memory operations

Compliance Features:
- GDPR-compatible data minimization
- Right to be forgotten (complete user data deletion)
- Data portability (JSON export)
- Transparent data processing logs

============================================================================
                              Future Planning
============================================================================

Short-term Goals:
- Add memory export/import functionality
- Support multi-user memory isolation
- Add memory visualization interface

Long-term Goals:
- Support vector database persistence (Pinecone/Weaviate)
- Implement distributed memory system
- Add memory security and privacy protection

============================================================================
                              Support & Contact
============================================================================

For technical support or questions:
- Check existing documentation
- Use /memory-status API to check system status
- Review logs for error information
- Contact development team for advanced issues

============================================================================
                              End of Document
============================================================================

This documentation provides a comprehensive overview of the SMOz system 
architecture, requirements, and development guidelines. The system is designed 
to be scalable, maintainable, and user-friendly while providing sophisticated 
AI conversation capabilities with advanced memory management.

Last Updated: 2025-01-27
Document Version: 1.0
