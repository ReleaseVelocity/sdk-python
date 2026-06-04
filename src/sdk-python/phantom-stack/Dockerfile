FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no .git
# repo, so we provide a pretend version (consumed by setuptools_scm / hatch-vcs).
ARG STRANDS_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${STRANDS_VERSION}

# The default test matrix ([all,dev]) is pure-Python wheels and needs no
# system build/runtime libraries, so the slim base is sufficient. Versioning
# uses the pretend version above instead of git, so git is not required.

WORKDIR /app

# Layer caching: copy only build-relevant manifests first.
COPY pyproject.toml README.md ./

# Install the project in editable/dev mode with all extras + dev tooling.
# Source isn't present yet, so create the package dir so the editable install
# resolves; full source is copied next and overrides this.
RUN mkdir -p src/strands && touch src/strands/__init__.py \
    && pip install --no-cache-dir -e ".[all,dev]"

# Copy the full source tree.
COPY . .

# Re-run install to pick up entry points / metadata against the real source.
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
