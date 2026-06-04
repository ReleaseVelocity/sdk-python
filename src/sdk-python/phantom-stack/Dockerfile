FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree has no .git
# directory, so provide an explicit fallback version to the build backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# System packages: PortAudio headers are needed if the optional bidi extra
# (pyaudio) is ever installed; git is handy for development.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first to leverage Docker layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so an editable install can resolve metadata,
# then install all runtime + model + dev/test dependencies.
COPY src/ ./src/

RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["bash"]
