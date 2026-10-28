# Running bolt.diy locally

This repository is the original StackBlitz bolt.new. **bolt.diy** is a separate community fork ([stackblitz-labs/bolt.diy](https://github.com/stackblitz-labs/bolt.diy)) with multi-provider LLM support. It lives in its own clone, not in this repo. This doc records how it was set up locally on Windows 11 so the steps can be repeated.

## Prerequisites

- **Node.js 22.12.0 or newer.** bolt.diy's `engines` field requires it. The setup used Node v24.22.1.
- **pnpm 9.15.9**, the version in bolt.diy's `packageManager` field. Enable it with corepack:

  ```bash
  corepack enable pnpm
  corepack prepare pnpm@9.15.9 --activate
  ```

- **git**
- **An LLM provider API key**, or a local model runner such as Ollama or LM Studio.

## Setup

```bash
git clone https://github.com/stackblitz-labs/bolt.diy.git
cd bolt.diy
pnpm install
```

`pnpm install` also runs `husky` through the `prepare` script. The first install took about 3 minutes.

## Configure a provider

Create a `.env.local` file in the bolt.diy root. It is gitignored. Uncomment the line for your provider and paste the key after the `=`, with no quotes or spaces:

```bash
ANTHROPIC_API_KEY=sk-ant-...
```

The variable names come from the `apiTokenKey` field in `app/lib/modules/llm/providers/*.ts`. Some are unusual, so check there if a key isn't picked up. For example, the HuggingFace key is `HuggingFace_API_KEY`, with mixed case.

Local runners need a base URL instead of a key:

```bash
OLLAMA_API_BASE_URL=http://127.0.0.1:11434
LMSTUDIO_API_BASE_URL=http://127.0.0.1:1234
```

You can also enter a key in the bolt.diy UI. The UI stores it in browser localStorage, and it overrides the value in `.env.local`. Use the file if the key should survive clearing browser data.

## Run

```bash
pnpm dev
```

Open http://localhost:5173. `.env.local` is read only at startup, so restart the server after every change to it.

On first load you get a welcome wizard, which asks for a name and an avatar. The first page load may also log `504 (Outdated Optimize Dep)` errors while Vite re-optimizes dependencies. Reload the page. If the errors persist, stop the server, delete `node_modules/.vite`, and run `pnpm dev` again.

## Troubleshooting

- **Chat fails right away.** The key is missing, misnamed, or was added without a restart. Check the variable name against the provider file and restart the server.
- **Port 5173 is already in use.** Stop whatever is holding the port, or edit `PORT` in `.env.local`.

## Notes for this repo

Nothing in bolt.new depends on bolt.diy. This doc is only a record of the local setup.
