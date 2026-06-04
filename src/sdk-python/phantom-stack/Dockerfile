FROM public.ecr.aws/docker/library/python:3.12-slim

# hatch-vcs determines the version from git tags. The source tree is not a git
# repository in this environment, so provide a pretend version to the build
# backend (hatch-vcs delegates to setuptools-scm's env var).
ENV SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Create the package layout so an editable install can resolve packages.
COPY src ./src

# Install the project in editable/development mode with ALL optional
# dependencies plus the dev/test tooling.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the remaining project files (tests, docs, configs).
COPY . .

CMD ["sleep", "infinity"]
