FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the source is copied
# without a .git directory, provide a pretend version so the build succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install of the package).
COPY src ./src

RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
