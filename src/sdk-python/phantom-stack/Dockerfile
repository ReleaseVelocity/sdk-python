FROM python:3.12-slim

# Install git (required by hatch-vcs for version determination)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for layer caching
COPY pyproject.toml README.md ./

# hatch-vcs determines version from git tags; the project source has no .git.
# Initialize a repo with a tag so editable install + version detection succeed.
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ
COPY docs ./docs
COPY LICENSE NOTICE ./

RUN git init -q \
    && git config user.email "dev@example.com" \
    && git config user.name "dev" \
    && git add -A \
    && git commit -qm "init" \
    && git tag v1.0.0

# Upgrade pip and install the project in editable/development mode with all
# optional dependency groups plus the dev/test toolchain.
RUN pip install --no-cache-dir --upgrade pip \
    && pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
