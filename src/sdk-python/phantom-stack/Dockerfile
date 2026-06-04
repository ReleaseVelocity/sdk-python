FROM python:3.12-slim

# git for hatch-vcs build backend metadata resolution
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs derives version from git tags; provide a fallback so build works
# without tagged git history.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1

# Install build tooling.
RUN pip install --upgrade pip hatchling hatch-vcs

# Copy the full source.
COPY . .

# Install the project in editable/development mode with ALL runtime extras
# ([all]) plus dev/test tooling ([dev]). This mirrors the project's
# hatch-test env (features = ["all"]) and the pytest config, which ignores
# the experimental bidi suite. bidi extras (pyaudio/smithy) are intentionally
# not part of [all] and out of the default test surface.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
