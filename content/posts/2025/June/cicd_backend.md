+++
date = '2025-06-23T21:03:07Z'
draft = false
title = 'DevOps study app: The Back-End'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

My GitHub repository for the project: <https://github.com/MorielMauni/devops-study-app>

### UV

- **Projects:** help manage Python code spanning multiple files. 
  - Create a new project with: `uv init`.
  - Creates following files: ".python-version", "README.MD", "main.py", and "pyproject.toml".
- **Dependencies**: When you import a library to the project, the project is depend on it. 
  - `uv add <library-name> `.
- **Python Package**: a Python project must be build to be installed. 
  - Add commands to the project.
  - Distribute the project to others.
  - Use a "src" and "test" layout.

#### Building the backend with UV

```
mkdir src && cd src
```

```
uv init --package backend
```

Tree:

```bash
/workspaces/devops-study-app/src main*
❯ tree
.
└── backend
    ├── pyproject.toml
    ├── README.md
    └── src
        └── backend
            └── __init__.py
```

src/backend/pyproject.toml

```toml
[project]
name = "study-tracker-backend"
version = "0.0.0"
description = "Backend API for tracking study time for DevOps certifications"
readme = "README.md"
authors = [{ name = "Moriel Mauni", email = "morielmauni@gmail.com" }]
requires-python = ">=3.13"
dependencies = []
│
[project.scripts]
study-tracker-api = "backend.main:main"

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"
```

The directory that going to be attached to this module:

```
devops-study-app/src/backend/src/backend
```

Create a main.py in this path and paste this: 
**NOTE**: The goal is being a DevOps note a Python developer.

### Python code 

#### main.py
```python
from fastapi import FastAPI, HTTPException, Query
from fastapi.middleware.cors import CORSMiddleware
import uvicorn
import logging
from typing import List, Optional

from .models import StudySession, StudySessionCreate, Stats
from .storage import save_session, get_all_sessions, get_sessions_by_tag, get_statistics
from .config import (
    APP_NAME,
    API_HOST,
    API_PORT,
    CORS_ALLOW_ORIGINS,
    CORS_ALLOW_CREDENTIALS,
    CORS_ALLOW_METHODS,
    CORS_ALLOW_HEADERS,
)

# Configure logging
logging.basicConfig(
    level=logging.INFO, format="%(asctime)s - %(name)s - %(levelname)s - %(message)s"
)
logger = logging.getLogger(__name__)

# Create FastAPI application
app = FastAPI(
    title=APP_NAME,
    description="API for tracking study time for DevOps studies",
)

# Configure CORS
app.add_middleware(
    CORSMiddleware,
    allow_origins=CORS_ALLOW_ORIGINS,
    allow_credentials=CORS_ALLOW_CREDENTIALS,
    allow_methods=CORS_ALLOW_METHODS,
    allow_headers=CORS_ALLOW_HEADERS,
)


@app.get("/")
async def root():
    """Root endpoint returning API information"""
    return {"message": "DevOps Study Tracker API"}


@app.get("/health")
async def health():
    """Health endpoint for kubernetes probes"""
    return {"status": "healthy"}


@app.post("/sessions", response_model=StudySession)
async def create_session(session: StudySessionCreate):
    """Create a new study session"""
    logger.info(
        f"Creating new session: {session.minutes} minutes with tag: {session.tag}"
    )
    try:
        new_session = save_session(session)
        return new_session
    except Exception as e:
        logger.error(f"Error creating session: {str(e)}")
        raise HTTPException(status_code=500, detail=f"Error creating session: {str(e)}")


@app.get("/sessions", response_model=List[StudySession])
async def read_sessions(
    tag: Optional[str] = Query(None, description="Filter sessions by tag"),
):
    """Get all study sessions, optionally filtered by tag"""
    try:
        if tag:
            logger.info(f"Fetching sessions with tag: {tag}")
            return get_sessions_by_tag(tag)
        else:
            logger.info("Fetching all sessions")
            return get_all_sessions()
    except Exception as e:
        logger.error(f"Error fetching sessions: {str(e)}")
        raise HTTPException(
            status_code=500, detail=f"Error fetching sessions: {str(e)}"
        )


@app.get("/stats", response_model=Stats)
async def read_stats():
    """Get aggregated statistics about study sessions"""
    logger.info("Fetching statistics")
    try:
        return get_statistics()
    except Exception as e:
        logger.error(f"Error fetching statistics: {str(e)}")
        raise HTTPException(
            status_code=500, detail=f"Error fetching statistics: {str(e)}"
        )


# Marker for CI pipeline
# This comment is used to trigger the CI pipeline when changes are made to this file.


def main():
    """Entry point for running the API server"""
    logger.info(f"Starting {APP_NAME} API")
    uvicorn.run("backend.main:app", host=API_HOST, port=API_PORT, reload=True)


if __name__ == "__main__":
    main()
```

#### config.py
```python
import os

# Application settings
APP_NAME = os.getenv("APP_NAME", "DevOps Study Tracker")

# API Configuration
API_HOST = os.getenv("API_HOST", "0.0.0.0")
API_PORT = int(os.getenv("API_PORT", "22112"))

# Data Storage Configuration
DATA_DIR = os.getenv(
    "DATA_DIR",
    os.path.join(os.path.dirname(os.path.dirname(os.path.abspath(__file__))), "data"),
)


# CORS Configuration
# Environment variables for CORS lists should be provided as comma-separated values
# For example: CORS_ALLOW_ORIGINS=https://example.com,https://api.example.com
# Using "*" will be converted to ["*"] for wildcard access
def parse_list_env(env_name, default="*"):
    value = os.getenv(env_name, default)
    if value == "*":
        return ["*"]
    return [item.strip() for item in value.split(",")]


CORS_ALLOW_ORIGINS = parse_list_env("CORS_ALLOW_ORIGINS")
CORS_ALLOW_METHODS = parse_list_env("CORS_ALLOW_METHODS")
CORS_ALLOW_HEADERS = parse_list_env("CORS_ALLOW_HEADERS")
CORS_ALLOW_CREDENTIALS = os.getenv("CORS_ALLOW_CREDENTIALS", "True").lower() == "true"
```

models.py
```python
from pydantic import BaseModel, Field
from datetime import datetime
from typing import Dict


class StudySessionCreate(BaseModel):
    """Model for creating a new study session"""

    minutes: int = Field(..., gt=0, description="Study duration in minutes")
    tag: str = Field(
        ...,
        min_length=1,
        description="Tag for the study session (e.g., certification name, topic)",
    )


class StudySession(StudySessionCreate):
    """Complete study session model including generated fields"""

    id: str = Field(..., description="Unique identifier for the session")
    timestamp: datetime = Field(
        ..., description="Timestamp of when the session was created"
    )


class Stats(BaseModel):
    """Model for aggregated statistics"""

    total_time: int = Field(..., description="Total study time in minutes")
    time_by_tag: Dict[str, int] = Field(..., description="Study time grouped by tag")
    total_sessions: int = Field(..., description="Total number of study sessions")
    sessions_by_tag: Dict[str, int] = Field(
        ..., description="Number of sessions grouped by tag"
    )
```

#### storage.py
```python
import csv
import os
import uuid
from datetime import datetime
from typing import List, Dict
import logging

from .models import StudySession, StudySessionCreate, Stats
from .config import DATA_DIR

# Configure logging
logger = logging.getLogger(__name__)

# Constants
SESSIONS_FILE = os.path.join(DATA_DIR, "sessions.csv")

# Ensure data directory exists
os.makedirs(DATA_DIR, exist_ok=True)

# CSV headers
CSV_HEADERS = ["id", "timestamp", "minutes", "tag"]


def _create_csv_if_not_exists():
    """Create the CSV file with headers if it doesn't exist"""
    if not os.path.exists(SESSIONS_FILE):
        logger.info(f"Creating new sessions CSV file at {SESSIONS_FILE}")
        with open(SESSIONS_FILE, "w", newline="") as f:
            writer = csv.DictWriter(f, fieldnames=CSV_HEADERS)
            writer.writeheader()


def save_session(session: StudySessionCreate) -> StudySession:
    """Save a new study session to the CSV file"""
    _create_csv_if_not_exists()

    # Create a complete StudySession with generated fields
    new_session = StudySession(
        id=str(uuid.uuid4()),
        timestamp=datetime.now(),
        minutes=session.minutes,
        tag=session.tag,
    )

    # Append to CSV file
    with open(SESSIONS_FILE, "a", newline="") as f:
        writer = csv.DictWriter(f, fieldnames=CSV_HEADERS)
        writer.writerow(
            {
                "id": new_session.id,
                "timestamp": new_session.timestamp.isoformat(),
                "minutes": new_session.minutes,
                "tag": new_session.tag,
            }
        )

    logger.info(f"Saved new session with ID {new_session.id}")
    return new_session


def get_all_sessions() -> List[StudySession]:
    """Retrieve all study sessions from the CSV file"""
    _create_csv_if_not_exists()
    sessions = []

    with open(SESSIONS_FILE, "r", newline="") as f:
        reader = csv.DictReader(f)
        for row in reader:
            sessions.append(
                StudySession(
                    id=row["id"],
                    timestamp=datetime.fromisoformat(row["timestamp"]),
                    minutes=int(row["minutes"]),
                    tag=row["tag"],
                )
            )

    logger.info(f"Retrieved {len(sessions)} sessions")
    return sessions


def get_sessions_by_tag(tag: str) -> List[StudySession]:
    """Retrieve study sessions filtered by tag"""
    all_sessions = get_all_sessions()
    filtered_sessions = [
        session for session in all_sessions if session.tag.lower() == tag.lower()
    ]
    logger.info(f"Retrieved {len(filtered_sessions)} sessions with tag '{tag}'")
    return filtered_sessions


def get_statistics() -> Stats:
    """Calculate aggregated statistics from all sessions"""
    sessions = get_all_sessions()

    # Calculate total minutes
    total_minutes = sum(session.minutes for session in sessions)

    # Calculate minutes by tag
    time_by_tag: Dict[str, int] = {}
    sessions_by_tag: Dict[str, int] = {}

    for session in sessions:
        tag = session.tag
        if tag not in time_by_tag:
            time_by_tag[tag] = 0
            sessions_by_tag[tag] = 0

        time_by_tag[tag] += session.minutes
        sessions_by_tag[tag] += 1

    stats = Stats(
        total_time=total_minutes,
        time_by_tag=time_by_tag,
        total_sessions=len(sessions),
        sessions_by_tag=sessions_by_tag,
    )

    logger.info(
        f"Calculated statistics: {total_minutes} minutes across {len(sessions)} sessions"
    )
    return stats
```

#### __ init__.py
```python
"""
DevOps Study Tracker API

A FastAPI application for tracking study time for DevOps certifications.
"""

from .models import StudySession, StudySessionCreate, Stats
from .storage import save_session, get_all_sessions, get_sessions_by_tag, get_statistics

# Expose main app for ASGI servers
from .main import app

# Define public API
__all__ = [
    "StudySession",
    "StudySessionCreate",
    "Stats",
    "save_session",
    "get_all_sessions",
    "get_sessions_by_tag",
    "get_statistics",
    "app",
]
```


### Adding dependencies

Add this to "pyproject.toml"
```toml
[tool.hatch.build.targets.wheel]
packages = ["src/backend"]
```

```
uv add fastapi
```

```
uv add uvicorn
```

```
uv add httpx
```

If we will look in "pyproject.toml" again, we will see it's added the packages we downloaded:
```toml
dependencies = [
    "fastapi>=0.115.13",
    "httpx>=0.28.1",
    "uvicorn>=0.34.3",
]
```

### Run the Python package for the first time

To run it we first need to use:
```
uv sync --locked --no-editable
```

- **sync**: means to install all the dependencies.
- **--locked**: uv.lock file -> the place the dependencies are.
- **-no-editable**: without the option to edit.

Now:
```
uv run study-tracker-api
```
 
 The results:
```bash 
curl localhost:22112
{"message":"DevOps Study Tracker API"}
```

## This is part 2/5, I will update as I go.
