FROM python:3.12-slim

# hatch-vcs derives the package version from git tags. Provide an explicit
# fallback version so the build works without a tagged git repository.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# Some optional dependencies (e.g. pyaudio) need build tooling / portaudio.
# We install only the bidi-all extra optionally; core 'all' + 'dev' do not
# require these, so keep the image lean and add git for hatch-vcs.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Layer caching: copy only the build manifest first.
COPY pyproject.toml README.md ./

# Initialize a git repo with a tag so hatch-vcs can resolve a version, then
# install the full set of dependencies (all model providers + dev/test tools).
# Copy the rest of the source needed for an editable install.
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

RUN git init -q . \
    && git config user.email "dev@example.com" \
    && git config user.name "dev" \
    && git add -A \
    && git commit -qm "snapshot" \
    && git tag v1.0.0 \
    && pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
