FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree shipped here has
# no .git directory, so provide an explicit version to the build backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0.dev0 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0.dev0 \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0.dev0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy the full source tree and install the project in editable/development
# mode with all runtime extras plus the dev/test toolchain (pytest, ruff,
# mypy, moto, etc.).
COPY . .
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
