# Development image for locke. The full ruby image already has curl, git,
# and the compilers and libpq headers native gems need.
FROM ruby:4.0.7
RUN apt-get update \
 && apt-get install -y --no-install-recommends ripgrep \
 && rm -rf /var/lib/apt/lists/*

# Node, npm and npx, at the version in the repo's .node-version.
COPY --from=node:24.22.0-slim /usr/local/bin/node /usr/local/bin/node
COPY --from=node:24.22.0-slim /usr/local/lib/node_modules /usr/local/lib/node_modules
RUN ln -s ../lib/node_modules/npm/bin/npm-cli.js /usr/local/bin/npm \
 && ln -s ../lib/node_modules/npm/bin/npx-cli.js /usr/local/bin/npx \
 && node --version && npx --version

# The system libraries Playwright's Chromium needs for system specs. The
# browsers themselves are installed by entrypoint.sh, at the version in
# package-lock.json, into the locke_playwright volume.
RUN npx -y playwright@1.64.0 install-deps chromium \
 && rm -rf /var/lib/apt/lists/* /root/.npm

# Claude Code, started with `neptuno claude locke`. Its sign-in and
# settings live in /root/.claude, which compose mounts from .neptuno/claude
# so they survive rebuilds and are shared by every repo.
RUN curl -fsSL https://claude.ai/install.sh -o /tmp/install-claude.sh \
 && bash /tmp/install-claude.sh \
 && rm /tmp/install-claude.sh \
 && /root/.local/bin/claude --version
ENV PATH="/root/.local/bin:$PATH" CLAUDE_CONFIG_DIR=/root/.claude
WORKDIR /app

# bundle install and db:prepare run when the container starts; Neptuno waits
# for them before starting the Procfile's processes.
COPY entrypoint.sh /usr/local/bin/entrypoint.sh
ENTRYPOINT ["sh", "/usr/local/bin/entrypoint.sh"]
