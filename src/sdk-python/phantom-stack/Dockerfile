FROM python:3.12-slim

# hatch-vcs/setuptools_scm reads version from git tags. The source tree has no
# .git history, so provide a pretend version to allow an editable install.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for layer caching
COPY pyproject.toml README.md ./

# Copy the rest of the source
COPY . .

# Install the project in editable/development mode with ALL extras + dev/test tools.
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
