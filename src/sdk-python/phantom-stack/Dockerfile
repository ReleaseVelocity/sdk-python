FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the source tree may not
# include a .git directory, tell setuptools_scm/hatch-vcs to use a fixed
# version so the editable install succeeds without git metadata.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1
ENV PYTHONUNBUFFERED=1

WORKDIR /app

# No system packages are required: all Python deps ship as prebuilt wheels and
# the package version is supplied via SETUPTOOLS_SCM_PRETEND_VERSION (so git is
# not needed). portaudio is only required by the optional, test-ignored bidi
# feature, so it is intentionally omitted.

# Copy the dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-resolve dependencies against the manifest (without the local package)
# so they are cached even before the full source is copied.
RUN pip install --upgrade pip

# Copy the full project source.
COPY . .

# Install the project in editable/development mode with all runtime extras
# plus the dev/test toolchain (pytest, moto, ruff, mypy, etc.).
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
