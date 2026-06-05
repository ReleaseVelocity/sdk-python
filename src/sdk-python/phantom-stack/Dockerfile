FROM python:3.13-slim

# hatch-vcs/setuptools_scm needs a version; repo has no git tags in this env.
# Provide a pretend version so the editable build succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# portaudio is required to build/run the optional bidi extra (pyaudio),
# but the default install path does not need it. Install build basics only
# if native compilation is required. Try slim first.
WORKDIR /app

# Copy dependency manifest + sources needed for an editable install.
# pyproject + README (referenced by readme) + source package.
COPY pyproject.toml README.md ./
COPY src ./src

# Editable install with all model-provider extras + dev/test tooling.
RUN pip install --upgrade pip && \
    pip install -e ".[all]" && \
    pip install -e ".[dev]"

# Bring in the rest of the project (tests, configs).
COPY . .

CMD ["sleep", "infinity"]
