FROM python:3.12-slim

WORKDIR /app

# hatch-vcs derives the package version from git tags. The build context may not
# include git history, so provide a fallback version that hatch-vcs honors. This
# avoids needing git/apt at build time (the build network has no outbound DNS).
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV HATCH_BUILD_HOOKS_ENABLE=1

# Copy the full source (editable install needs the package tree present)
COPY . .

# Install project in editable/dev mode with ALL optional + dev dependencies
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
