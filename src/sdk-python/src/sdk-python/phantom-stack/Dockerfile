FROM python:3.12-slim

# hatch-vcs/setuptools_scm needs a version; repo has no .git in this env.
# Provide a pretend version so an editable install succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

WORKDIR /app

# Minimal system deps. git is useful for hatch-vcs; build tools kept minimal.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

RUN pip install --no-cache-dir --upgrade pip

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source needed for the editable install.
COPY src/ ./src/

# Install the project in editable/development mode with ALL extras + dev tools.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
