+++
date = '2025-06-30T21:49:42Z'
draft = false
title = 'DevOps Study app: GitHub Actions- Part 2'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

### Tests

#### \_\_ init_\_.py

```python
```

#### test_config.py

```python
import os
from unittest import mock

from backend.config import (
    APP_NAME,
    API_HOST,
    API_PORT,
    DATA_DIR,
    CORS_ALLOW_ORIGINS,
    CORS_ALLOW_METHODS,
    CORS_ALLOW_HEADERS,
    CORS_ALLOW_CREDENTIALS,
)


def test_settings_default_values():
    """Test that Settings has the expected default values."""
    assert APP_NAME == "DevOps Study Tracker"
    assert API_HOST == "0.0.0.0"
    assert API_PORT == 22112
    assert DATA_DIR.endswith("data")

    # Test CORS default values
    assert CORS_ALLOW_ORIGINS == ["*"]
    assert CORS_ALLOW_METHODS == ["*"]
    assert CORS_ALLOW_HEADERS == ["*"]
    assert CORS_ALLOW_CREDENTIALS is True


@mock.patch.dict(
    os.environ,
    {
        "API_HOST": "127.0.0.1",
        "API_PORT": "9000",
        "CORS_ALLOW_ORIGINS": "https://example.com,https://api.example.com",
        "CORS_ALLOW_METHODS": "GET,POST,PUT",
        "CORS_ALLOW_HEADERS": "Content-Type,Authorization",
        "CORS_ALLOW_CREDENTIALS": "false",
    },
)
def test_settings_from_env():
    """Test that Settings loads values from environment variables."""
    # Force reload of the config module to get updated environment variables
    import importlib
    import backend.config

    importlib.reload(backend.config)

    # Import the settings again after reload
    from backend.config import (
        API_HOST,
        API_PORT,
        CORS_ALLOW_ORIGINS,
        CORS_ALLOW_METHODS,
        CORS_ALLOW_HEADERS,
        CORS_ALLOW_CREDENTIALS,
    )

    assert API_HOST == "127.0.0.1"
    assert API_PORT == 9000

    # Test CORS values from environment
    assert CORS_ALLOW_ORIGINS == [
        "https://example.com",
        "https://api.example.com",
    ]
    assert CORS_ALLOW_METHODS == ["GET", "POST", "PUT"]
    assert CORS_ALLOW_HEADERS == ["Content-Type", "Authorization"]
    assert CORS_ALLOW_CREDENTIALS is False
```

#### test_main.py

```python
import pytest
import pytest_asyncio
from httpx import AsyncClient
import os
import csv
from unittest import mock

# Import the FastAPI app and models from the proper package path
from backend.main import app
from backend.models import StudySessionCreate
from backend.storage import save_session

# Import the storage module itself
import backend.storage as storage_module

# Define the path for a temporary test data file using relative paths
TEST_DATA_FILE = os.path.join(os.path.dirname(__file__), "test_sessions.csv")


@pytest.fixture(scope="session", autouse=True)
def setup_test_environment():
    """Fixture to set up the test environment before any tests run."""
    # Ensure the test data directory exists
    os.makedirs(os.path.dirname(TEST_DATA_FILE), exist_ok=True)

    # Ensure the test data file does not exist before tests
    if os.path.exists(TEST_DATA_FILE):
        os.remove(TEST_DATA_FILE)
    # Override the SESSIONS_FILE path in the storage module for testing
    storage_module.SESSIONS_FILE = TEST_DATA_FILE  # Use the imported module alias
    yield
    # Clean up the test data file after all tests run
    if os.path.exists(TEST_DATA_FILE):
        os.remove(TEST_DATA_FILE)


@pytest_asyncio.fixture(scope="function")
async def client():
    """Provides an async test client for the FastAPI app."""
    # Use ASGITransport instead of direct app parameter - newer httpx client syntax
    from httpx import ASGITransport

    transport = ASGITransport(app=app)
    async with AsyncClient(transport=transport, base_url="http://test") as client:
        yield client


@pytest.fixture(scope="function", autouse=True)
def reset_test_data():
    """Fixture to reset the test data file before each test function."""
    if os.path.exists(TEST_DATA_FILE):
        os.remove(TEST_DATA_FILE)

    # Create an empty file with headers using CSV, similar to how the main app does it
    with open(TEST_DATA_FILE, "w", newline="") as f:
        writer = csv.DictWriter(f, fieldnames=["id", "timestamp", "minutes", "tag"])
        writer.writeheader()


@pytest.mark.asyncio
async def test_root(client: AsyncClient):
    """Test the root endpoint."""
    response = await client.get("/")
    assert response.status_code == 200
    assert response.json() == {"message": "DevOps Study Tracker API"}


@pytest.mark.asyncio
async def test_create_session(client: AsyncClient):
    """Test creating a new study session."""
    session_data = {"minutes": 30, "tag": "AWS"}
    response = await client.post("/sessions", json=session_data)
    assert response.status_code == 200
    data = response.json()
    assert data["minutes"] == 30
    assert data["tag"] == "AWS"
    assert "id" in data
    assert "timestamp" in data

    # Verify data was saved using CSV
    with open(TEST_DATA_FILE, "r", newline="") as f:
        reader = list(csv.DictReader(f))
        assert len(reader) == 1
        assert int(reader[0]["minutes"]) == 30
        assert reader[0]["tag"] == "AWS"


@pytest.mark.asyncio
async def test_read_sessions_empty(client: AsyncClient):
    """Test reading sessions when none exist."""
    response = await client.get("/sessions")
    assert response.status_code == 200
    assert response.json() == []


@pytest.mark.asyncio
async def test_read_sessions_all(client: AsyncClient):
    """Test reading all sessions."""
    # Create some sessions first using the storage function directly for setup
    save_session(
        StudySessionCreate(minutes=25, tag="Kubernetes")
    )  # Use imported save_session
    save_session(StudySessionCreate(minutes=50, tag="AWS"))  # Use imported save_session

    response = await client.get("/sessions")
    assert response.status_code == 200
    data = response.json()
    assert len(data) == 2
    assert data[0]["tag"] == "Kubernetes"
    assert data[1]["tag"] == "AWS"


@pytest.mark.asyncio
async def test_read_sessions_by_tag(client: AsyncClient):
    """Test reading sessions filtered by tag."""
    save_session(
        StudySessionCreate(minutes=25, tag="Kubernetes")
    )  # Use imported save_session
    save_session(StudySessionCreate(minutes=50, tag="AWS"))  # Use imported save_session
    save_session(
        StudySessionCreate(minutes=15, tag="Kubernetes")
    )  # Use imported save_session

    response = await client.get("/sessions?tag=Kubernetes")
    assert response.status_code == 200
    data = response.json()
    assert len(data) == 2
    assert all(item["tag"] == "Kubernetes" for item in data)

    response = await client.get("/sessions?tag=AWS")
    assert response.status_code == 200
    data = response.json()
    assert len(data) == 1
    assert data[0]["tag"] == "AWS"

    response = await client.get("/sessions?tag=NonExistent")
    assert response.status_code == 200
    assert response.json() == []


@pytest.mark.asyncio
async def test_read_stats(client: AsyncClient):
    """Test reading statistics."""
    save_session(
        StudySessionCreate(minutes=25, tag="Kubernetes")
    )  # Use imported save_session
    save_session(StudySessionCreate(minutes=50, tag="AWS"))  # Use imported save_session
    save_session(
        StudySessionCreate(minutes=15, tag="Kubernetes")
    )  # Use imported save_session

    response = await client.get("/stats")
    assert response.status_code == 200
    data = response.json()
    assert data["total_sessions"] == 3
    assert data["total_time"] == 90  # 25 + 50 + 15
    assert "time_by_tag" in data
    assert data["sessions_by_tag"]["Kubernetes"] == 2
    assert data["sessions_by_tag"]["AWS"] == 1


@pytest.mark.asyncio
async def test_read_stats_empty(client: AsyncClient):
    """Test reading statistics when no sessions exist."""
    response = await client.get("/stats")
    assert response.status_code == 200
    data = response.json()
    assert data["total_sessions"] == 0
    assert data["total_time"] == 0
    assert data["time_by_tag"] == {}
    assert data["sessions_by_tag"] == {}


@pytest.mark.asyncio
async def test_error_handling_create_session(client: AsyncClient, monkeypatch):
    """Test error handling in create_session endpoint."""

    # Mock save_session to raise an exception
    def mock_save_session(*args, **kwargs):
        raise Exception("Test error")

    monkeypatch.setattr("backend.main.save_session", mock_save_session)

    session_data = {"minutes": 30, "tag": "AWS"}
    response = await client.post("/sessions", json=session_data)
    assert response.status_code == 500
    assert "Error creating session" in response.json()["detail"]


@pytest.mark.asyncio
async def test_error_handling_read_sessions(client: AsyncClient, monkeypatch):
    """Test error handling in read_sessions endpoint."""

    # Mock get_all_sessions to raise an exception
    def mock_get_all_sessions(*args, **kwargs):
        raise Exception("Test error")

    monkeypatch.setattr("backend.main.get_all_sessions", mock_get_all_sessions)

    response = await client.get("/sessions")
    assert response.status_code == 500
    assert "Error fetching sessions" in response.json()["detail"]


@pytest.mark.asyncio
async def test_error_handling_read_sessions_by_tag(client: AsyncClient, monkeypatch):
    """Test error handling in read_sessions endpoint with tag filter."""

    # Mock get_sessions_by_tag to raise an exception
    def mock_get_sessions_by_tag(*args, **kwargs):
        raise Exception("Test error")

    monkeypatch.setattr("backend.main.get_sessions_by_tag", mock_get_sessions_by_tag)

    response = await client.get("/sessions?tag=AWS")
    assert response.status_code == 500
    assert "Error fetching sessions" in response.json()["detail"]


@pytest.mark.asyncio
async def test_error_handling_read_stats(client: AsyncClient, monkeypatch):
    """Test error handling in read_stats endpoint."""

    # Mock get_statistics to raise an exception
    def mock_get_statistics(*args, **kwargs):
        raise Exception("Test error")

    monkeypatch.setattr("backend.main.get_statistics", mock_get_statistics)

    response = await client.get("/stats")
    assert response.status_code == 500
    assert "Error fetching statistics" in response.json()["detail"]


# Test for the main function
def test_main_function(monkeypatch):
    """Test the main() function that starts the uvicorn server."""
    # Mock uvicorn.run to prevent actually starting the server
    mock_run = mock.Mock()
    monkeypatch.setattr("uvicorn.run", mock_run)

    # Call the main function
    from backend.main import main

    main()

    # Check that uvicorn.run was called with the expected arguments
    mock_run.assert_called_once_with(
        "backend.main:app", host="0.0.0.0", port=22112, reload=True
    )
```

- Checking for test file.
	- If don't exist, creating a test file.
- Setting up a test client for fastapi.
- Calling functions to test the client:
	- test_root()
	- test_create_session()

#### test_storage.py

```python
import pytest
from datetime import datetime
import os
import uuid
import csv

# Import functions and variables from storage.py using new package structure
from backend.storage import (
    save_session,
    get_all_sessions,
    get_sessions_by_tag,
    get_statistics,
)
from backend.models import StudySessionCreate, StudySession

# Import the storage module itself
import backend.storage as storage_module


# Define the path for a temporary test data file using relative paths
TEST_DATA_FILE = os.path.join(os.path.dirname(__file__), "test_storage_sessions.csv")


@pytest.fixture(scope="module", autouse=True)
def setup_test_data_file():
    """Fixture to set up the test data file path for the storage module tests."""
    # Ensure the test data directory exists
    os.makedirs(os.path.dirname(TEST_DATA_FILE), exist_ok=True)

    original_data_file = storage_module.SESSIONS_FILE  # Use alias
    # Override the SESSIONS_FILE path in the storage module for testing
    storage_module.SESSIONS_FILE = TEST_DATA_FILE  # Use alias
    yield
    # Restore the original data file path and clean up
    storage_module.SESSIONS_FILE = original_data_file  # Use alias
    if os.path.exists(TEST_DATA_FILE):
        os.remove(TEST_DATA_FILE)


@pytest.fixture(scope="function", autouse=True)
def reset_test_data():
    """Fixture to reset the test data file before each test function."""
    if os.path.exists(TEST_DATA_FILE):
        os.remove(TEST_DATA_FILE)
    # Create an empty file with headers using CSV
    with open(TEST_DATA_FILE, "w", newline="") as f:
        writer = csv.DictWriter(f, fieldnames=["id", "timestamp", "minutes", "tag"])
        writer.writeheader()


def test_save_session():
    """Test saving a single session."""
    session_create = StudySessionCreate(minutes=45, tag="Terraform")
    saved_session = save_session(session_create)

    assert isinstance(saved_session, StudySession)
    assert saved_session.minutes == 45
    assert saved_session.tag == "Terraform"
    assert isinstance(saved_session.id, str)  # ID is stored as string in the model
    assert uuid.UUID(saved_session.id)  # Verify it's a valid UUID string
    assert isinstance(saved_session.timestamp, datetime)

    # Verify with CSV reader
    with open(TEST_DATA_FILE, "r", newline="") as f:
        reader = list(csv.DictReader(f))
        assert len(reader) == 1
        assert int(reader[0]["minutes"]) == 45
        assert reader[0]["tag"] == "Terraform"
        assert saved_session.id == reader[0]["id"]  # Compare as strings


def test_save_multiple_sessions():
    """Test saving multiple sessions sequentially."""
    save_session(StudySessionCreate(minutes=30, tag="Docker"))
    save_session(StudySessionCreate(minutes=60, tag="Python"))

    with open(TEST_DATA_FILE, "r", newline="") as f:
        reader = list(csv.DictReader(f))
        assert len(reader) == 2
        assert reader[0]["tag"] == "Docker"
        assert reader[1]["tag"] == "Python"


def test_get_all_sessions():
    """Test retrieving all sessions."""
    save_session(StudySessionCreate(minutes=20, tag="Git"))
    save_session(StudySessionCreate(minutes=40, tag="CI/CD"))

    sessions = get_all_sessions()
    assert len(sessions) == 2
    assert isinstance(sessions[0], StudySession)
    assert sessions[0].tag == "Git"
    assert sessions[1].tag == "CI/CD"


def test_get_all_sessions_empty():
    """Test retrieving all sessions when none exist."""
    sessions = get_all_sessions()
    assert sessions == []


def test_get_sessions_by_tag():
    """Test retrieving sessions filtered by tag."""
    save_session(StudySessionCreate(minutes=25, tag="AWS"))
    save_session(StudySessionCreate(minutes=55, tag="Azure"))
    save_session(StudySessionCreate(minutes=35, tag="AWS"))

    aws_sessions = get_sessions_by_tag("AWS")
    assert len(aws_sessions) == 2
    assert all(s.tag == "AWS" for s in aws_sessions)

    azure_sessions = get_sessions_by_tag("Azure")
    assert len(azure_sessions) == 1
    assert azure_sessions[0].tag == "Azure"

    gcp_sessions = get_sessions_by_tag("GCP")
    assert gcp_sessions == []


def test_get_statistics():
    """Test calculating statistics."""
    save_session(StudySessionCreate(minutes=10, tag="Linux"))
    save_session(StudySessionCreate(minutes=20, tag="Networking"))
    save_session(StudySessionCreate(minutes=30, tag="Linux"))

    stats = get_statistics()
    assert stats.total_sessions == 3
    assert stats.total_time == 60  # 10 + 20 + 30
    # Calculate average manually if needed
    average_minutes = stats.total_time / stats.total_sessions
    assert average_minutes == 20.0
    assert stats.sessions_by_tag == {"Linux": 2, "Networking": 1}


def test_get_statistics_empty():
    """Test calculating statistics when no sessions exist."""
    stats = get_statistics()
    assert stats.total_sessions == 0
    assert stats.total_time == 0
    # Can't calculate average with zero sessions
    assert stats.time_by_tag == {}
    assert stats.sessions_by_tag == {}


def test_create_and_read_sessions():
    """Test creating sessions and then reading them back."""
    # Create test CSV file with two sessions
    with open(TEST_DATA_FILE, "w", newline="") as f:
        writer = csv.DictWriter(f, fieldnames=["id", "timestamp", "minutes", "tag"])
        writer.writeheader()
        writer.writerow(
            {
                "id": str(uuid.uuid4()),
                "timestamp": datetime.now().isoformat(),
                "minutes": 15,
                "tag": "TestTag1",
            }
        )
        writer.writerow(
            {
                "id": str(uuid.uuid4()),
                "timestamp": datetime.now().isoformat(),
                "minutes": 45,
                "tag": "TestTag2",
            }
        )

    # Read sessions
    sessions = get_all_sessions()
    assert len(sessions) == 2
    assert sessions[0].tag == "TestTag1"
    assert sessions[1].minutes == 45


def test_file_not_exists_handling():
    """Test handling when the data file doesn't exist."""
    # Rename the test file temporarily
    if os.path.exists(TEST_DATA_FILE):
        os.rename(TEST_DATA_FILE, f"{TEST_DATA_FILE}.bak")

    try:
        # This should create the data file
        sessions = get_all_sessions()
        assert sessions == []

        # Verify the file was created with headers
        with open(TEST_DATA_FILE, "r", newline="") as f:
            content = f.read()
            assert "id,timestamp,minutes,tag" in content
    finally:
        # Restore the original file if it existed
        if os.path.exists(f"{TEST_DATA_FILE}.bak"):
            if os.path.exists(TEST_DATA_FILE):
                os.remove(TEST_DATA_FILE)
            os.rename(f"{TEST_DATA_FILE}.bak", TEST_DATA_FILE)
```

### Add dependencies

```
uv add pytest
```

```
uv add pytest_asyncio
```

Now we can run ```pytest``` to test out files:
```python
================================== test session starts ===================================
platform linux -- Python 3.13.5, pytest-8.4.1, pluggy-1.6.0
rootdir: /workspaces/devops-study-app/src/backend
configfile: pyproject.toml
plugins: anyio-4.9.0, asyncio-1.0.0
asyncio: mode=Mode.STRICT, asyncio_default_fixture_loop_scope=None, asyncio_default_test_loop_scope=function
collected 23 items

tests/test_config.py ..                                                            [  8%]
tests/test_main.py ............                                                    [ 60%]
tests/test_storage.py .........                                                    [100%]

=================================== 23 passed in 1.27s ===================================
```

#### Troubleshoot

If you get this error:
```pyton
The configuration option "asyncio_default_fixture_loop_scoop" is unset.
```

We set it in pyproject.toml:
```
[tool.pytest.ini_options]
asyncio_default_fixture_loop_scope = "function"
```

And the output of ```pytest``` again will look like this:
```python
================================== test session starts ===================================
platform linux -- Python 3.13.5, pytest-8.4.1, pluggy-1.6.0
rootdir: /workspaces/devops-study-app/src/backend
configfile: pyproject.toml
plugins: anyio-4.9.0, asyncio-1.0.0
asyncio: mode=Mode.STRICT, asyncio_default_fixture_loop_scope=function, asyncio_default_test_loop_scope=function
collected 23 items

tests/test_config.py ..                                                            [  8%]
tests/test_main.py ............                                                    [ 60%]
tests/test_storage.py .........                                                    [100%]

=================================== 23 passed in 0.89s ===================================
```

### Add to the pipeline

Add the step for pytest in the backend-tests.yaml

/.github/workflows/backend-tests.yaml
```
      - name: Run tests with coverage
        run: |
          uv run pytest
```

Commit, Push, on GitHub squash and merge.
Switch back to main with ```git switch main``` and pull the updated main ```git pull```. 
make a new branch ```git switch -c ci/add-coverage-testing```
### Test coverage

Under /.github/workflows/backend-tests.yaml remove the last step we put and add this instead:

```
      - name: Run tests with coverage
        run: |
          uv run pytest tests/ -v --cov=src/backend --cov-report=xml --cov-fail-under=80

      - name: Upload coverage report
        uses: actions/upload-artifact@v4
        with:
          name: coverage-report
          path: ./src/backend/coverage.xml
          retention-days: 7
        if: ${{ always() }}

      - name: Coverage Report
        uses: irongut/CodeCoverageSummary@v1.3.0
        with:
          filename: ./src/backend/coverage.xml
          badge: true
          format: markdown
          output: both

      - name: Add Coverage PR Comment
        uses: marocchino/sticky-pull-request-comment@v2
        if: github.event_name == 'pull_request'
        with:
          header: test-coverage
          recreate: true
          path: code-coverage-results.md
```

- ```uv run pytest tests/ -v --cov=src/backend --cov-report=xml --cov-fail-under=80```
	- ```uv run pytest tests```: pytest for the test directory .
	- ```-v --cov=src/backend```: targeting the entire directory.
	- ```--cov-report=xml```: make the report as a xml file.
	- ```--cov-fail-under=80```: under 80 is a fail.

#### Test it locally
run it in /src/backend
```
uv run pytest tests/ -v --cov=src/backend --cov-report=xml --cov-fail-under=80
```

Output:
```bash
 Built study-tracker-backend @ file:///workspaces/devops-study-app/src/backend
Uninstalled 1 package in 9ms
░░░░░░░░░░░░░░░░░░░░ [0/1] Installing wheels...                                           warning: Failed to hardlink files; falling back to full copy. This may lead to degraded performance.
         If the cache and target directories are on different filesystems, hardlinking may not be supported.
         If this is intentional, set `export UV_LINK_MODE=copy` or use `--link-mode=copy` to suppress this warning.
Installed 1 package in 32ms
ERROR: usage: pytest [options] [file_or_dir] [file_or_dir] [...]
pytest: error: unrecognized arguments: --cov=src/backend --cov-report=xml --cov-fail-under=80
  inifile: /workspaces/devops-study-app/src/backend/pyproject.toml
  rootdir: /workspaces/devops-study-app/src/backend
```

#### Troubleshoot

The error here is:
```bash
error: unrecognized arguments: --cov
```

We need to install pytest-cov
```
uv add pytest-cov
```

Now, the output is:
```bash
================================== test session starts ===================================
platform linux -- Python 3.13.5, pytest-8.4.1, pluggy-1.6.0 -- /workspaces/devops-study-app/src/backend/.venv/bin/python3
cachedir: .pytest_cache
rootdir: /workspaces/devops-study-app/src/backend
configfile: pyproject.toml
plugins: anyio-4.9.0, asyncio-1.0.0, cov-6.2.1
asyncio: mode=Mode.STRICT, asyncio_default_fixture_loop_scope=function, asyncio_default_test_loop_scope=function
collected 23 items

tests/test_config.py::test_settings_default_values PASSED                          [  4%]
tests/test_config.py::test_settings_from_env PASSED                                [  8%]
tests/test_main.py::test_root PASSED                                               [ 13%]
tests/test_main.py::test_create_session PASSED                                     [ 17%]
tests/test_main.py::test_read_sessions_empty PASSED                                [ 21%]
tests/test_main.py::test_read_sessions_all PASSED                                  [ 26%]
tests/test_main.py::test_read_sessions_by_tag PASSED                               [ 30%]
tests/test_main.py::test_read_stats PASSED                                         [ 34%]
tests/test_main.py::test_read_stats_empty PASSED                                   [ 39%]
tests/test_main.py::test_error_handling_create_session PASSED                      [ 43%]
tests/test_main.py::test_error_handling_read_sessions PASSED                       [ 47%]
tests/test_main.py::test_error_handling_read_sessions_by_tag PASSED                [ 52%]
tests/test_main.py::test_error_handling_read_stats PASSED                          [ 56%]
tests/test_main.py::test_main_function PASSED                                      [ 60%]
tests/test_storage.py::test_save_session PASSED                                    [ 65%]
tests/test_storage.py::test_save_multiple_sessions PASSED                          [ 69%]
tests/test_storage.py::test_get_all_sessions PASSED                                [ 73%]
tests/test_storage.py::test_get_all_sessions_empty PASSED                          [ 78%]
tests/test_storage.py::test_get_sessions_by_tag PASSED                             [ 82%]
tests/test_storage.py::test_get_statistics PASSED                                  [ 86%]
tests/test_storage.py::test_get_statistics_empty PASSED                            [ 91%]
tests/test_storage.py::test_create_and_read_sessions PASSED                        [ 95%]
tests/test_storage.py::test_file_not_exists_handling PASSED                        [100%]

===================================== tests coverage =====================================
____________________ coverage: platform linux, python 3.13.5-final-0 _____________________

Coverage XML written to file coverage.xml
Required test coverage of 80% reached. Total coverage: 98.55%
=================================== 23 passed in 1.50s ===================================
```

We got 98.55%.
