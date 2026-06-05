FROM python:3.13-slim

# hatch-vcs derives the version from git tags. Since the build context may not
# contain git history, provide a pretend version so the build succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0.dev0

WORKDIR /app

# System deps: git for hatch-vcs, and build basics for any wheels needing compilation.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install).
COPY src ./src

# Install the project in editable/development mode with all extras + dev tools.
RUN pip install --no-cache-dir --upgrade pip \
    && pip install --no-cache-dir -e ".[all,dev]"

# Copy tests and remaining project files.
COPY tests ./tests
COPY tests_integ ./tests_integ
COPY . .

CMD ["sleep", "infinity"]
