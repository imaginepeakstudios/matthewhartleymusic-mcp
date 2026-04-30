<p align="center">
  <img src="https://experience.matthewhartleymusic.com/the-time-is-now-ai-music-experience-matthew-hartley.jpg" alt="Matthew Hartley — The Time Is Now: A Journey in Chapters · Powered by Harmonic Wave" width="100%" />
</p>

# Matthew Hartley Music — MCP Server

> **The first artist-owned MCP server.** Discover, narrate, and stream a continuous, story-driven album from inside any AI client that speaks the <a href="https://modelcontextprotocol.io" target="_blank" rel="noopener noreferrer">Model Context Protocol</a>.

**Live endpoint** (public, no auth):
```
https://matthewhartleymusic.com/wp-json/mcp/v1/message
```

---

## What it is

This MCP server exposes **The Time Is Now: A Journey in Chapters** — Matthew Hartley's debut album — as a structured, AI-native experience. Twelve songs across six chapters, each with lyrics, emotional narrative, version recordings, and direct streaming links. Designed so any AI client can:

- **Discover the catalog** — list songs and chapters with rich metadata
- **Tell the story behind each song** — short and full narratives, lyrics, mood tags
- **Curate a sequenced listening experience** — six listening modes (`late_night`, `devotional`, `acoustic_focus`, `cinematic`, `quiet_listening`, `full_journey`) with scene directives for visual presentation
- **Stream the audio directly** — fresh signed Cloudflare R2 URLs minted on demand, ~4-hour TTL with a refresh tool to keep playback uninterrupted

---

## Quick install

### Claude Desktop

Add to `~/Library/Application Support/Claude/claude_desktop_config.json` (macOS) or `%APPDATA%\Claude\claude_desktop_config.json` (Windows):

```json
{
  "mcpServers": {
    "matthew-hartley-music": {
      "type": "http",
      "url": "https://matthewhartleymusic.com/wp-json/mcp/v1/message"
    }
  }
}
```

Restart Claude Desktop. The server appears in your tool list.

### Claude Code (CLI)

```bash
claude mcp add matthew-hartley-music https://matthewhartleymusic.com/wp-json/mcp/v1/message
```

### Test the connection

```bash
curl -X POST https://matthewhartleymusic.com/wp-json/mcp/v1/message \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list"}'
```

You should get back JSON listing all 8 tools.

---

## Tools

| Tool | Purpose |
|---|---|
| `list_songs` | List all songs (filter by chapter slug or release status) |
| `get_song` | Fetch one song by ID or slug — lyrics, quotes, cover art, streaming links, chapter assignment |
| `list_chapters` | List all chapters with numeral, name, year range, mood tags, song count |
| `get_chapter` | Fetch one chapter with all songs and full ACF narrative |
| `get_artist` | Artist profile + biographical context |
| `get_experience` | One-shot curated playlist for a listening mode — songs, scenes, fresh stream URLs |
| `get_experience_prompt` | Returns the narration prompt template AI agents use to host listening sessions |
| `refresh_stream_urls` | Mint fresh signed audio URLs in bulk, before existing URLs expire |

All tools return JSON-RPC 2.0 responses. Streaming-capable songs gate streaming by `release_status`; chapter narratives gate by `chapter_released`.

---

## Example prompts to try

After installing, ask Claude:

> *"List Matthew Hartley's songs and tell me the story behind 'Heaven's Calling.'"*

> *"Build me a quiet_listening experience using the get_experience tool. Then narrate it like a storyteller introducing each song."*

> *"What's the journey of his album, chapter by chapter?"*

> *"Play me Chapter VI and explain what changed in his life during that season."*

The `get_experience` tool is the highest-leverage call — one request returns a fully-sequenced playlist with stream URLs and visual scene cues, ready for a listening session.

---

## The reference player

A dedicated browser player is built directly on this MCP:

**<a href="https://experience.matthewhartleymusic.com" target="_blank" rel="noopener noreferrer">Harmonic Wave Experience Media Player</a>** — A cinematic, audio-reactive listening experience with DJ voiceovers, LRC-synced lyric overlays, and per-song visual palettes. Nothing to install — just open and listen.

---

## Architecture

```
┌─────────────────────────────┐
│  Any AI client (Claude,     │
│  ChatGPT, Cursor, etc.)     │
└────────────┬────────────────┘
             │ JSON-RPC over HTTP
             ▼
┌─────────────────────────────┐
│ matthewhartleymusic.com     │
│ /wp-json/mcp/v1/message     │
│ (WordPress mu-plugin)       │
└────────────┬────────────────┘
             │ signs URLs at request time
             ▼
┌─────────────────────────────┐
│  Cloudflare R2              │
│  (private bucket — signed   │
│   URLs only, 4-hour TTL)    │
└─────────────────────────────┘
```

- WordPress hosts the catalog (custom post types `song` and `artist`, taxonomy `chapter`, ACF fields for lyrics/stories/streaming links)
- Audio files live in a private Cloudflare R2 bucket, accessed via AWS Sig V4 presigned URLs minted on demand
- The MCP endpoint is fully public — no auth required. Per-song streaming is gated server-side by release status.

---

## About Matthew Hartley

Matthew Hartley is a Christian rock songwriter from Sarasota, FL — and a 25+ year software industry veteran whose career spans engineering, architecture, sales, and executive leadership, with multiple USPTO patent filings to his name. He leads <a href="https://imaginepeakstudios.com/" target="_blank" rel="noopener noreferrer">Imagine Peak Studios</a>, the studio behind this MCP and the Harmonic Wave platform. *The Time Is Now: A Journey in Chapters* is a twelve-song album written across twenty-five years (1999–2026) — six chapters tracing innocence and heartbreak, awakening, breaking the cycle, love and commitment, collapse and calling, and grounded faith.

- **Website:** <a href="https://matthewhartleymusic.com" target="_blank" rel="noopener noreferrer">matthewhartleymusic.com</a>
- **Experience player:** <a href="https://experience.matthewhartleymusic.com" target="_blank" rel="noopener noreferrer">experience.matthewhartleymusic.com</a>
- **Spotify, Apple Music, YouTube, SoundCloud:** all linked per-song in the MCP responses

---

## Powered by Harmonic Wave

This server is built on the <a href="https://harmonicwave.ai" target="_blank" rel="noopener noreferrer">Harmonic Wave</a> platform — for content creators making their work AI-native.

The platform also includes a **<a href="https://github.com/imaginepeakstudios/harmonic-wave-plugin" target="_blank" rel="noopener noreferrer">Harmonic Wave plugin for Claude Code</a>** that turns prompts into custom media players for catalogs on the platform.

---

## License

The documentation in this repository is licensed under the [Apache License 2.0](LICENSE). The MCP server's WordPress mu-plugin source code is proprietary and is not included in this repository. The music, lyrics, story content, and audio recordings exposed by the MCP are © Imagine Peak Studios — all rights reserved.
