FROM python:3.12-slim

# System deps: git (hatch-vcs/version), build tools and portaudio for optional pyaudio (bidi)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs derives the version from git tags. There is no .git in the build
# context, so provide an explicit fallback version.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install of the wheel package)
COPY src ./src

# Install the project in editable/dev mode with all optional + dev/test deps.
RUN pip install --upgrade pip && \
    pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs)
COPY . .

CMD ["sleep", "infinity"]
