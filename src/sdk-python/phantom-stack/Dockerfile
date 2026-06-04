FROM python:3.12-slim

# git is commonly required by dev tooling (pre-commit, commitizen, hatch-vcs)
RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs determines the version from git tags. The build context has no
# git metadata, so provide an explicit fallback version.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=0.0.0

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy source so editable install can resolve the package
COPY src ./src

# Upgrade pip tooling and install the project in editable/dev mode
# with all optional features plus the dev/test toolchain.
RUN pip install --no-cache-dir --upgrade pip setuptools wheel \
    && pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, etc.)
COPY . .

CMD ["sleep", "infinity"]
