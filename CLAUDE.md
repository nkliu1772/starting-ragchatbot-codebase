# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Course Materials RAG (Retrieval-Augmented Generation) System built with Python/FastAPI backend and vanilla JavaScript frontend. It enables semantic search and AI-powered responses for course materials using ChromaDB and Anthropic's Claude.

## Development Commands

**Setup:**
```bash
# Install uv package manager (if not installed)
curl -LsSf https://astral.sh/uv/install.sh | sh

# Install dependencies
uv sync

# Environment setup
cp .env.example .env
# Add ANTHROPIC_API_KEY=your_key_here to .env
```

**Running:**
```bash
# Quick start (recommended)
./run.sh

# Manual start
cd backend && uv run uvicorn app:app --reload --port 8000
```

**Access Points:**
- Web Interface: http://localhost:8000
- API Documentation: http://localhost:8000/docs

## Architecture Overview

**Core System Design:**
- **RAG Orchestrator** (`backend/rag_system.py`) - Central coordinator managing all components
- **Tool-Based AI Architecture** - Uses Anthropic's tool calling with CourseSearchTool for controlled search
- **Dual Vector Store Design** - ChromaDB with separate collections for course metadata (`course_catalog`) and content chunks (`course_content`)

**Key Components:**
- `backend/app.py` - FastAPI application with CORS/middleware
- `backend/vector_store.py` - ChromaDB integration for semantic search  
- `backend/ai_generator.py` - Anthropic Claude API with tool calling
- `backend/document_processor.py` - Text chunking and course parsing
- `backend/session_manager.py` - Conversation history management
- `backend/search_tools.py` - Tool-based search with source tracking

**Data Flow:**
1. User query → Session management → RAG orchestration
2. Claude processes with tool definitions → CourseSearchTool executes vector search
3. Search results formatted with course/lesson context → Claude generates response
4. Sources tracked and returned to frontend → Session history updated

## Document Processing

**Expected Course Data Format:**
```
Course Title: [title]
Course Link: [url]
Course Instructor: [instructor]
Lesson N: [title]
Lesson Link: [url]
[content...]
```

**Processing Pipeline:**
- Sentence-based chunking (800 chars, 100 overlap)
- Dual indexing: metadata → `course_catalog`, content → `course_content`
- Smart course name resolution using vector similarity

## Configuration

**Centralized config** (`backend/config.py`):
- `ANTHROPIC_MODEL`: "claude-sonnet-4-20250514"
- `EMBEDDING_MODEL`: "all-MiniLM-L6-v2"
- `CHUNK_SIZE`: 800, `CHUNK_OVERLAP`: 100
- `MAX_RESULTS`: 5, `MAX_HISTORY`: 2

**Environment Variables:**
- `ANTHROPIC_API_KEY` - Required for Claude API access

## Frontend Architecture

**Technology Stack:**
- Vanilla JavaScript with modern ES6+ features
- Marked.js for markdown rendering
- CSS Grid/Flexbox with custom properties for dark theme
- Single-page application with real-time course statistics

**Key Features:**
- Session-based conversation tracking
- Source attribution with collapsible sections
- Suggested questions for user guidance
- Loading states and error handling