FROM python:3.12-slim

# Pretend version for hatch-vcs since no .git tags are present in the build context
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# System deps: git for hatch-vcs, build tools + portaudio for the optional bidi/pyaudio extras
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        g++ \
        make \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for layer caching
COPY pyproject.toml README.md ./

# Install project in editable/dev mode with all + dev dependencies.
# Create the package dir so editable install metadata resolves before full source copy.
RUN mkdir -p src/strands && touch src/strands/__init__.py \
    && pip install --no-cache-dir -e ".[all,dev]"

# Copy full source
COPY . .

# Reinstall editable to pick up the real package contents/metadata
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
