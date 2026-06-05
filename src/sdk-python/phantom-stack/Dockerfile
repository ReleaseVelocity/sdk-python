FROM python:3.12-slim

# Install git (required by hatch-vcs to determine the package version from tags)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the rest of the source
COPY . /app

# hatch-vcs derives the version from git tags. The project is shipped without a
# .git directory, so initialize a throwaway repo with a tag to satisfy the build.
RUN if [ ! -d /app/.git ]; then \
        git init -q /app && \
        git -c user.email=dev@example.com -c user.name=dev add -A && \
        git -c user.email=dev@example.com -c user.name=dev commit -q -m "phantom-stack build" && \
        git -c user.email=dev@example.com -c user.name=dev tag v0.0.0 ; \
    fi

# Install the project in editable/development mode with ALL optional deps and dev tools
RUN pip install --no-cache-dir --upgrade pip && \
    pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
