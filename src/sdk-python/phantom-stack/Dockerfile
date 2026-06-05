FROM python:3.12-slim

# hatch-vcs (setuptools-scm under the hood) derives the version from git tags.
# The build context has no .git metadata, so initialize a throwaway git repo
# with a tag inside the image so the version can be detected at install time.
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# Some optional dependencies (e.g. pyaudio for the bidi extra) need build tools
# and portaudio headers. Install minimal native build deps up front.
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    git \
    portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source needed for the editable install.
COPY src ./src

# hatch-vcs needs git metadata; create a minimal repo with a version tag.
RUN git init -q . \
    && git config user.email "dev@example.com" \
    && git config user.name "dev" \
    && git add -A \
    && git commit -qm "dev" \
    && git tag v0.0.0

# Install the project in editable/development mode with all + dev dependencies.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
