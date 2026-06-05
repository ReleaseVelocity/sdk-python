FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context may not
# include git metadata, provide a fallback version so the editable install works.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

RUN pip install --upgrade pip

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the source package (needed for editable install build backend).
COPY src ./src

# Install the project in editable/development mode with ALL optional deps and
# the dev toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["bash"]
