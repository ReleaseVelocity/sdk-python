FROM python:3.12-slim

# hatch-vcs/setuptools_scm derives the version from git tags. Since the build
# context has no .git history, provide a pretend version so installation works.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# Copy the full project. hatchling needs the package present at install time
# to build a correct editable install.
COPY . .

# Install the project in editable/development mode with ALL optional features
# plus the dev/test tooling so a developer can run tests and linters.
RUN --mount=type=cache,target=/root/.cache/pip \
    pip install -e ".[all,dev]"

CMD ["bash"]
