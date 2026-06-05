FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context may not
# include a populated git history, provide a fallback version via setuptools_scm.
ARG PKG_VERSION=0.1.0.dev0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION}
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# git is referenced by hatch-vcs / pre-commit; keep the image minimal otherwise.
RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Layer caching: copy dependency manifest first.
COPY pyproject.toml README.md ./

# Bring in the package source needed for an editable install.
COPY src ./src

# Install the project in development mode with all optional + dev dependencies.
# "[all]" pulls every model provider; "[dev]" pulls test/lint tooling.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
