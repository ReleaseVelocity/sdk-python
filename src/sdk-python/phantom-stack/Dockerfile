FROM python:3.12-slim

# git is required by hatch-vcs / hatchling as a build-time helper for VCS-based versioning
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy the whole project (library + tests)
COPY . .

# hatch-vcs (built on setuptools_scm) determines the version from git tags.
# There is no .git here, so provide a pretend version to satisfy the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

# Editable install with ALL feature extras plus dev/test dependencies.
RUN python -m pip install --upgrade pip \
    && pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
