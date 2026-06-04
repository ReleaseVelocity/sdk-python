FROM python:3.11-slim

# hatch-vcs derives the version from git tags. The build context has no .git,
# so provide a pretend version to allow editable install to succeed.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+docker \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# git is occasionally needed by build backends / tooling; keep image lean otherwise.
RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so the build backend can resolve packages during
# the dependency-resolution step (editable install needs the real source too,
# so the full source is copied next and reinstalled).
COPY src ./src

# Install the SDK in editable/development mode with ALL feature extras and the
# dev (test/lint) toolchain so a developer can run tests and linters.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

# Reinstall to pick up any metadata; editable install keeps source linked.
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
