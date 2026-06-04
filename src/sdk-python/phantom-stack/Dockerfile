FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context may not
# include a .git directory, provide a fallback version via setuptools_scm.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+docker
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# git is needed by hatch-vcs build backend; build tools for any native deps.
RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source needed for an editable install.
COPY src ./src

# Install the project in editable/development mode with ALL optional
# dependencies plus the dev/test tooling.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the remainder of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
