+++
date = '2025-07-09T20:24:16Z'
draft = false
title = 'DevOps Study app: GitHub Actions- Front-end'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

The project GitHub repository: [https://github.com/MorielMauni/devops-study-app]

Adding the test files:

conftest.py
```python
import pytest
from flask import template_rendered
from contextlib import contextmanager
from frontend.main import app


@contextmanager
def captured_templates(app):
    """Context manager to track templates rendered by the Flask app.
    Allows test to assert on which templates were rendered and their contexts.
    """
    recorded = []

    def record(sender, template, context, **kwargs):
        recorded.append((template, context))

    template_rendered.connect(record, app)
    try:
        yield recorded
    finally:
        template_rendered.disconnect(record, app)


@pytest.fixture
def client():
    """Test client for the Flask application"""
    app.config["TESTING"] = True
    with app.test_client() as client:
        yield client
```

test_main.py
```python
import json
import requests
import responses
from conftest import captured_templates
from frontend.main import app


@responses.activate
def test_index_page(client):
    """Test the index route when API returns sessions"""
    # Sample response data that mimics what the backend would return
    sample_sessions = [
        {
            "id": "12345",
            "timestamp": "2025-04-23T10:15:30Z",
            "minutes": 45,
            "tag": "Python",
        },
        {
            "id": "67890",
            "timestamp": "2025-04-22T14:20:00Z",
            "minutes": 30,
            "tag": "AWS",
        },
    ]

    # Mock the API response for sessions
    responses.add(
        responses.GET,
        "http://localhost:22112/sessions",
        json=sample_sessions,
        status=200,
    )

    # Use the captured_templates context manager to verify rendered template
    with captured_templates(app) as templates:
        response = client.get("/")

        # Check the response
        assert response.status_code == 200

        # Check that the correct template was rendered
        assert len(templates) == 1
        template, context = templates[0]
        assert template.name == "index.html"

        # Check that sessions were passed to the template with correct formatting
        rendered_sessions = context["sessions"]
        assert len(rendered_sessions) == 2

        # Verify first session data
        assert rendered_sessions[0]["id"] == "12345"
        assert rendered_sessions[0]["minutes"] == 45
        assert rendered_sessions[0]["tag"] == "Python"
        assert rendered_sessions[0]["formatted_date"] == "2025-04-23 10:15"

        # Verify the sessions are sorted by timestamp (newest first)
        assert (
            rendered_sessions[0]["timestamp_obj"]
            > rendered_sessions[1]["timestamp_obj"]
        )


@responses.activate
def test_index_page_api_error(client):
    """Test the index route when API returns an error"""
    # Mock the API response to return an error
    responses.add(
        responses.GET,
        "http://localhost:22112/sessions",
        json={"error": "Internal server error"},
        status=500,
    )

    with captured_templates(app) as templates:
        response = client.get("/")

        # Check the response
        assert response.status_code == 200

        # Check that the correct template was rendered with empty sessions
        assert len(templates) == 1
        template, context = templates[0]
        assert template.name == "index.html"
        assert context["sessions"] == []


@responses.activate
def test_add_session_valid(client):
    """Test adding a valid study session"""
    # Mock the API response for creating a session
    responses.add(
        responses.POST,
        "http://localhost:22112/sessions",
        json={
            "id": "abcde",
            "timestamp": "2025-04-24T09:30:00Z",
            "minutes": 60,
            "tag": "Docker",
        },
        status=200,
    )

    # Send a POST request to add a new session
    response = client.post("/add_session", data={"minutes": "60", "tag": "Docker"})

    # Verify redirect to index page
    assert response.status_code == 302
    assert response.headers["Location"] == "/"

    # Verify the API was called with correct payload
    request = responses.calls[0].request
    assert request.body is not None, "Request body should not be None"
    assert json.loads(request.body) == {"minutes": 60, "tag": "Docker"}


def test_add_session_invalid_minutes(client):
    """Test adding a study session with invalid minutes"""
    # Send a POST request with invalid minutes (non-numeric)
    response = client.post("/add_session", data={"minutes": "invalid", "tag": "Docker"})

    # Verify redirect to index page
    assert response.status_code == 302
    assert response.headers["Location"] == "/"


def test_add_session_empty_tag(client):
    """Test adding a study session with empty tag"""
    # Send a POST request with empty tag
    response = client.post("/add_session", data={"minutes": "30", "tag": ""})

    # Verify redirect to index page
    assert response.status_code == 302
    assert response.headers["Location"] == "/"


def test_add_session_zero_minutes(client):
    """Test adding a study session with zero minutes"""
    # Send a POST request with zero minutes
    response = client.post("/add_session", data={"minutes": "0", "tag": "Docker"})

    # Verify redirect to index page
    assert response.status_code == 302
    assert response.headers["Location"] == "/"


@responses.activate
def test_add_session_api_error(client):
    """Test adding a session when API returns an error"""
    # Mock the API to return an error
    responses.add(
        responses.POST,
        "http://localhost:22112/sessions",
        json={"error": "Server error"},
        status=500,
    )

    # Send a POST request to add a new session
    response = client.post("/add_session", data={"minutes": "45", "tag": "Terraform"})

    # Verify redirect to index page despite API error
    assert response.status_code == 302
    assert response.headers["Location"] == "/"


@responses.activate
def test_health_endpoint_api_healthy(client):
    """Test the health endpoint when the API is healthy"""
    # Mock the API health endpoint to return success
    responses.add(
        responses.GET,
        "http://localhost:22112/health",
        json={"status": "healthy"},
        status=200,
    )

    # Send a GET request to the health endpoint
    response = client.get("/health")

    # Verify response
    assert response.status_code == 200
    data = response.json
    assert data["status"] == "healthy"
    assert data["api_connectivity"] is True


@responses.activate
def test_health_endpoint_api_down(client):
    """Test the health endpoint when the API is down"""
    # Mock the API health endpoint to return a connection error
    responses.add(
        responses.GET,
        "http://localhost:22112/health",
        body=requests.RequestException("Connection error"),
    )

    # Send a GET request to the health endpoint
    response = client.get("/health")

    # Verify response - should return 503 when API is down
    assert response.status_code == 503
    data = response.json
    assert data["status"] == "unhealthy"
    assert data["api_connectivity"] is False
```

add dependencies: 
```
uv add pytest pytest_asyncio responses
```

Now when we run ```pytest``` we get:
```bash
================================== test session starts ===================================
platform linux -- Python 3.13.5, pytest-8.4.1, pluggy-1.6.0                               
rootdir: /workspaces/devops-study-app/src/frontend                                        
configfile: pyproject.toml                                                                
plugins: asyncio-1.0.0                                                                    
asyncio: mode=Mode.STRICT, asyncio_default_fixture_loop_scope=None, asyncio_default_test_loop_scope=function                                                                        
collected 9 items                                                                         
                                                                                          
tests/test_main.py .........                                                       [100%] 
                                                                                          
=================================== 9 passed in 0.31s ====================================
```

Let's add another uv dependency:
```
uv add pytest-cov
```

/.github/workflows/frontend-test.yaml
```yaml
name: Frontend Tests

on:
  pull_request:

jobs:
  check_paths:
    runs-on: ubuntu-latest
    outputs:
      should_run: ${{ steps.filter.outputs.frontend }}
    steps:
      - uses: actions/checkout@v4
      - uses: dorny/paths-filter@v3.0.2
        id: filter
        with:
          filters: |
            frontend:
              - 'src/frontend/**'
              - '.github/workflows/frontend-tests.yaml'

  test:
    name: Test Frontend
    needs: check_paths
    if: ${{ needs.check_paths.outputs.should_run == 'true' }}
    runs-on: ubuntu-latest
    defaults:
      run:
        working-directory: ./src/frontend

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Install UV
        uses: astral-sh/setup-uv@v5

      - name: Set up Python
        run: uv python install

      - name: Install dependencies
        run: |
          uv sync --locked --dev

      - name: Lint with Ruff
        run: |
          uv run ruff check --output-format=github --target-version=py313 src tests
          uv run ruff format --diff --check --target-version=py313 src tests

      - name: Run tests with coverage
        run: |
          uv run pytest tests/ -v --cov=src/frontend --cov-report=xml --cov-fail-under=80

      - name: Upload coverage report
        uses: actions/upload-artifact@v4
        with:
          name: frontend-coverage-report
          path: ./src/frontend/coverage.xml
          retention-days: 7
        if: ${{ always() }}

      - name: Coverage Report
        uses: irongut/CodeCoverageSummary@v1.3.0
        with:
          filename: ./src/frontend/coverage.xml
          badge: true
          format: markdown
          output: both

      - name: Add Coverage PR Comment
        uses: marocchino/sticky-pull-request-comment@v2
        if: github.event_name == 'pull_request'
        with:
          header: frontend-test-coverage
          recreate: true
          path: code-coverage-results.md

      - name: Build Docker image
        run: docker build -t frontend-app:latest .

      - name: Run Trivy vulnerability scanner
        uses: aquasecurity/trivy-action@0.30.0
        with:
          image-ref: "frontend-app:latest"
          format: "table"
          exit-code: "0"
          # exit-code: '1' # Fail the workflow if vulnerabilities are found.
          ignore-unfixed: true
          vuln-type: "os,library"
          severity: "CRITICAL,HIGH"
```

Let's run the tests:
```
uv run pytest tests/ -v --cov=src/frontend --cov-report=xml --cov-fail-under=80
```

Output:
```bash
================================= test session starts ===================================
platform linux -- Python 3.13.5, pytest-8.4.1, pluggy-1.6.0 -- /workspaces/devops-study-app/src/frontend/.venv/bin/python3                                                          
cachedir: .pytest_cache                                                                   
rootdir: /workspaces/devops-study-app/src/frontend                                        
configfile: pyproject.toml                                                                
plugins: asyncio-1.0.0, cov-6.2.1                                                         
asyncio: mode=Mode.STRICT, asyncio_default_fixture_loop_scope=None, asyncio_default_test_loop_scope=function                                                                        
collected 9 items                                                                         
                                                                                          
tests/test_main.py::test_index_page PASSED                                         [ 11%] 
tests/test_main.py::test_index_page_api_error PASSED                               [ 22%] 
tests/test_main.py::test_add_session_valid PASSED                                  [ 33%] 
tests/test_main.py::test_add_session_invalid_minutes PASSED                        [ 44%] 
tests/test_main.py::test_add_session_empty_tag PASSED                              [ 55%] 
tests/test_main.py::test_add_session_zero_minutes PASSED                           [ 66%] 
tests/test_main.py::test_add_session_api_error PASSED                              [ 77%] 
tests/test_main.py::test_health_endpoint_api_healthy PASSED                        [ 88%] 
tests/test_main.py::test_health_endpoint_api_down PASSED                           [100%] 
                                                                                          
===================================== tests coverage =====================================
____________________ coverage: platform linux, python 3.13.5-final-0 _____________________
                                                                                          
Coverage XML written to file coverage.xml                                                 
Required test coverage of 80% reached. Total coverage: 96.05%                             
=================================== 9 passed in 0.35s ====================================
```

Let's fix the dependencies:

```
uv add pytest pytest-cov responses ruff --dev 
```

pyproject.toml
```toml
[project]
name = "study-tracker-frontend"
version = "0.0.0"
description = "Frontend for DevOps Study Tracker application"
readme = "README.md"
authors = [{ name = "Mischa van den Burg", email = "mischa@gmail.com" }]
requires-python = ">=3.13"
dependencies = ["flask>=3.1.1", "requests>=2.32.4"]

[project.scripts]
study-tracker-frontend = "frontend.main:main"

[tool.hatch.build.targets.wheel]
packages = ["src/frontend"]

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[dependency-groups]
dev = [
    "pytest>=8.4.1",
    "pytest-cov>=6.2.1",
    "responses>=0.25.7",
    "ruff>=0.12.2",
]
```

If we check the build it's still 49 MB.

Now, we can commit, push, squash and merge. 

![GitHub-5](/images/cicd-testfronend.jpg)
