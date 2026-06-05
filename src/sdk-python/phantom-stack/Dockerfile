FROM python:3.12-slim

# System deps: git is required by hatch-vcs for versioning; build tools for native wheels (e.g. pyaudio)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# hatch-vcs needs git metadata to compute a version; create a placeholder repo/tag
# so editable install works even without the original .git history.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_ROOT_USER_ACTION=ignore

# Copy full source
COPY . .

# Ensure git history/tag exists for hatch-vcs version resolution
RUN git config --global --add safe.directory /app \
    && if [ ! -d .git ]; then \
         git init -q && git add -A && \
         git -c user.email=dev@local -c user.name=dev commit -qm init && \
         git tag v0.0.0; \
       fi

# Install project in editable/dev mode with ALL features + dev/test tooling
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
