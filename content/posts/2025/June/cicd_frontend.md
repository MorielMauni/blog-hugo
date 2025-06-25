+++
date = '2025-06-25T16:04:27Z'
draft = false
title = 'DevOps study app: The Front-End'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

My GitHub repository for the project: <https://github.com/MorielMauni/devops-study-app>

### Code 
main.py 
```python
from flask import Flask, render_template, request, redirect, url_for, jsonify
import requests
import logging
from datetime import datetime
import os
from typing import Dict, List, Any

# Configure logging
logging.basicConfig(
    level=logging.INFO, format="%(asctime)s - %(name)s - %(levelname)s - %(message)s"
)
logger = logging.getLogger(__name__)

# Create Flask application
app = Flask(__name__)

# Configuration
app.config["API_URL"] = os.getenv("API_URL", "http://localhost:22112")
app.config["API_TIMEOUT"] = int(os.getenv("API_TIMEOUT", "5"))
app.config["PORT"] = int(os.getenv("FRONTEND_PORT", "22111"))
app.config["DEBUG"] = os.getenv("FLASK_DEBUG", "False").lower() == "true"
app.config["HOST"] = os.getenv("FRONTEND_HOST", "0.0.0.0")


def format_session(session: Dict[str, Any]) -> Dict[str, Any]:
    """Format a session's timestamp for display"""
    timestamp = datetime.fromisoformat(session["timestamp"].replace("Z", "+00:00"))
    session["formatted_date"] = timestamp.strftime("%Y-%m-%d %H:%M")
    session["timestamp_obj"] = timestamp
    return session


# Helper functions for API calls
def get_sessions() -> List[Dict[str, Any]]:
    """Get all study sessions from the API"""
    try:
        response = requests.get(
            f"{app.config['API_URL']}/sessions", timeout=app.config["API_TIMEOUT"]
        )
        response.raise_for_status()
        sessions = response.json()

        # Format timestamps for display and sort
        sessions = [format_session(session) for session in sessions]

        # Sort sessions by timestamp, newest first
        sessions.sort(key=lambda x: x["timestamp_obj"], reverse=True)

        return sessions
    except requests.RequestException as e:
        logger.error(f"Error fetching sessions: {str(e)}")
        return []


def create_session(minutes: int, tag: str) -> bool:
    """Create a new study session via the API"""
    try:
        payload = {"minutes": minutes, "tag": tag}
        response = requests.post(
            f"{app.config['API_URL']}/sessions",
            json=payload,
            timeout=app.config["API_TIMEOUT"],
        )
        response.raise_for_status()
        return True
    except requests.RequestException as e:
        logger.error(f"Error creating session: {str(e)}")
        return False


# Routes
@app.route("/")
def index():
    """Homepage displaying study session form and list"""
    sessions = get_sessions()
    return render_template("index.html", sessions=sessions)


@app.route("/add_session", methods=["POST"])
def add_session():
    """Add a new study session"""
    try:
        minutes = int(request.form.get("minutes", 0))
        tag = request.form.get("tag", "").strip()

        if minutes <= 0:
            logger.warning("Attempted to add session with minutes <= 0")
            return redirect(url_for("index"))

        if not tag:
            logger.warning("Attempted to add session with empty tag")
            return redirect(url_for("index"))

        if create_session(minutes, tag):
            logger.info(f"Successfully added session: {minutes} mins, tag='{tag}'")
        else:
            logger.error(f"Failed to add session: {minutes} mins, tag='{tag}'")

    except ValueError:
        logger.error("Invalid input for minutes")

    return redirect(url_for("index"))


@app.route("/health")
def health():
    """Health check endpoint for monitoring"""
    try:
        # Attempt to connect to the API
        response = requests.get(
            f"{app.config['API_URL']}/health", timeout=app.config["API_TIMEOUT"]
        )
        api_status = response.status_code == 200
    except requests.RequestException:
        api_status = False

    status = "healthy" if api_status else "unhealthy"
    return jsonify(
        {"status": status, "api_connectivity": api_status}
    ), 200 if api_status else 503


def main():
    """Entry point for the application when run as a script"""
    logger.info("Starting DevOps Study Timer Frontend")
    app.run(host=app.config["HOST"], port=app.config["PORT"], debug=app.config["DEBUG"])


if __name__ == "__main__":
    main()
```

__ init__.py 
```python 
"""
DevOps Study Timer Frontend

A Flask web application for tracking study time for DevOps certifications.
"""

# Import and expose main functions and app
from .main import app, get_sessions, create_session

# Define public API
__all__ = ["app", "get_sessions", "create_session"]
```

/templates/index.html 
```html 
{% extends "base.html" %}

{% block title %}DevOps Study Tracker - Dashboard{% endblock %}

{% block content %}

<h2>Add Study Session</h2>
<form action="/add_session" method="post">
    <div>
        <label for="minutes">Study Time (minutes):</label>
        <input type="number" id="minutes" name="minutes" min="1" required>
    </div>
    <div>
        <label for="tag">Tag:</label>
        <input type="text" id="tag" name="tag" required>
    </div>
    <button type="submit">Add Session</button>
</form>

<h2>Study Sessions</h2>

{% if sessions %}
<table>
    <thead>
        <tr>
            <th>Date</th>
            <th>Tag</th>
            <th>Minutes</th>
        </tr>
    </thead>
    <tbody>
        {% for session in sessions %}
        <tr>
            <td>{{ session.formatted_date }}</td>
            <td>{{ session.tag }}</td>
            <td>{{ session.minutes }}</td>
        </tr>
        {% endfor %}
    </tbody>
</table>
{% else %}
<p>No study sessions recorded yet</p>
{% endif %}


{% endblock %}

{% block scripts %}
{% endblock %}
```

/templates/base.html
```html 
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{% block title %}DevOps Study Tracker{% endblock %}</title>
    <style>
        body {
            font-family: sans-serif;
            margin: 20px;
        }
        table {
            width: 80%; /* Make table wide */
            border-collapse: collapse; /* Remove gaps between cells */
            margin-top: 20px;
            margin-bottom: 20px;
        }
        th, td {
            border: 1px solid #ddd; /* Add simple borders */
            padding: 12px; /* Add padding for spacing */
            text-align: left;
        }
        th {
            background-color: #f2f2f2; /* Light grey background for headers */
        }
        form div {
            margin-bottom: 10px;
        }
        label {
            display: inline-block;
            width: 150px;
        }
        button {
            padding: 8px 15px;
            cursor: pointer;
        }
    </style>
    {% block head %}{% endblock %}
</head>
<body>
    <h1>DevOps Study Tracker</h1>
    {% block content %}{% endblock %}
    {% block scripts %}{% endblock %}
</body>
</html>
```

Add this to "pyproject.toml"
```toml
[tool.hatch.build.targets.wheel]
packages = ["src/frontend"]
```

#### Add dependency:
```
uv add flask
```

```
uv add requests
```

Run:
```
uv sync
```

Then:
```
uv sync --locked --no-editable
```

To run the project:
```
uv run study-tracker-frontend
```


