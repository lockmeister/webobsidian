# Obsidian Mobile Editor

A single-file, serverless HTML app for editing Obsidian vaults stored in private GitHub repositories. Optimized for mobile devices with PWA support for installation on iOS and Android.

## Features

- **Zero Build Steps**: Single HTML file with all dependencies via CDN
- **Mobile-First**: Touch-optimized UI, installable as PWA on iOS/Android
- **Obsidian Syntax Support**:
  - `[[Note Name]]` links (clickable, navigate between notes)
  - `![[image.png]]` embedded images
- **Real-time Preview**: Split-screen editor with live Markdown rendering
- **GitHub Integration**: Direct API access to private repositories
- **Conflict Detection**: Simple SHA-based conflict alerts (no auto-merge)
- **Dark Mode**: Obsidian-like aesthetics with gray/black palette

## Security Model

### Client-Side Only Architecture

This app uses a **client-side only** security model:

1. **No Backend Server**: The HTML file is served as a static file (e.g., via GitHub Pages)
2. **Local Token Storage**: Your GitHub Personal Access Token (PAT) is stored in browser `localStorage`
3. **Direct API Calls**: Your browser communicates directly with GitHub's API
4. **Zero Server Trust**: No intermediary server ever sees your token or data

### What This Means

**Safe:**
- Your token never leaves your browser except to talk to GitHub
- No database storing credentials
- No server-side code that could be compromised
- Fully auditable (public source code)

**Important Precautions:**
- Only use this app from **trusted sources** (verify the URL and code)
- Avoid using on **shared/public computers** (token persists in localStorage)
- If your browser is compromised, localStorage can be read (use device security)
- Anyone with physical access to your unlocked device could access the app

### Comparison to Alternatives

| Approach | Security | Complexity |
|----------|----------|------------|
| **This app (client-side PAT)** | High - no server to compromise | Low |
| Backend storing tokens | Low - single point of failure | High |
| OAuth flow | Higher - scoped permissions | Very High |

## Getting Started

### Prerequisites

1. A GitHub repository containing your Obsidian vault (can be private)
2. A GitHub Personal Access Token (PAT) with `repo` scope

   **What is a PAT?** A Personal Access Token is like a password that grants applications access to your GitHub account. Unlike your actual password, you can create multiple tokens with specific permissions and revoke them individually.

   **How to create one:**
   - Go to [Create a new Personal Access Token](https://github.com/settings/personal-access-tokens/new)
   - Give it a descriptive name (e.g., "Obsidian Mobile Editor")
   - Set expiration (or choose "No expiration" if you trust your device security)
   - Select the `repo` scope (full control of private repositories)
   - Click "Generate token"
   - Copy the token immediately (it won't be shown again)

### Using the App

1. **Open the HTML file** in any browser (or visit the hosted URL)
2. **First-time setup**:
   - Enter your GitHub Personal Access Token
   - Enter repository owner (your GitHub username)
   - Enter repository name
   - Click "Save & Continue"
3. **Browse files**: Use the sidebar to find and open `.md` files
4. **Edit**: Make changes in the left pane, see preview on the right
5. **Save**: Click "Save" to commit changes back to GitHub
6. **Mobile**: Use "Add to Home Screen" for app-like experience

### Installation as PWA

**iOS (Safari):**
1. Open the app in Safari
2. Tap the Share button
3. Select "Add to Home Screen"
4. The app will appear as an icon on your home screen

**Android (Chrome):**
1. Open the app in Chrome
2. Tap the menu (three dots)
3. Select "Add to Home Screen"
4. The app will appear as an icon in your app drawer

## Technical Details

### Stack
- **HTML5**: Semantic markup with PWA manifest
- **Tailwind CSS**: Via CDN for styling
- **Marked.js**: Via CDN for Markdown parsing
- **Vanilla JavaScript**: No frameworks, no build step

### GitHub API Usage

The app uses the following GitHub API endpoints:
- `GET /repos/{owner}/{repo}/git/trees/{sha}` - Fetch file tree
- `GET /repos/{owner}/{repo}/contents/{path}` - Fetch file content
- `PUT /repos/{owner}/{repo}/contents/{path}` - Save file changes

### Conflict Handling

When you save, the app sends the file's SHA hash to GitHub. If someone else modified the file (SHA mismatch), GitHub returns a 409 Conflict error.

**The app's response:**
- Alert: "Version conflict! Someone modified this file. Copy your text, refresh the page, and paste it back."
- **No automatic merge** - this prevents data loss from complex merge conflicts

### Limitations

- **No offline mode**: Requires internet connection to load/save files
- **No multi-file operations**: Edit one file at a time
- **No image upload**: Can display images from repo, but can't upload new ones
- **No real-time collaboration**: Conflicts handled via refresh-and-paste
- **No Obsidian plugins**: Only core Markdown + basic link/image syntax

## Deployment

### GitHub Pages (Recommended)

1. Create a new public repository
2. Add `obsidian-mobile-editor.html` to the repo
3. Go to Settings → Pages
4. Select "Deploy from branch" → `main` → `/` (root)
5. Save and wait for deployment
6. Your app will be available at `https://yourusername.github.io/repo-name/obsidian-mobile-editor.html`

**Why public repo is safe:**
- The code contains no secrets
- User tokens are stored client-side
- Anyone can audit the code for security

### Other Hosting Options

Any static file host works:
- Netlify
- Vercel
- Cloudflare Pages
- AWS S3 + CloudFront
- Your own web server

## Development

To modify the app:

1. Open `obsidian-mobile-editor.html` in your favorite editor
2. Make changes
3. Test by opening the file in a browser
4. No build step needed - it's just HTML/CSS/JS

### Key Functions

- `loadConfig()` / `saveConfig()`: localStorage management
- `loadFileTree()`: Fetch repo file structure
- `loadFile(path)`: Load specific file content
- `saveFile()`: Commit changes to GitHub
- `processObsidianSyntax()`: Convert `[[links]]` and `![[images]]`
- `updatePreview()`: Render Markdown preview

## FAQ

**Q: Is my GitHub token secure?**
A: Yes. It's stored in your browser's localStorage and only sent to GitHub's API. No server ever sees it.

**Q: Can I use this with public repositories?**
A: Yes, but you still need a PAT with `repo` scope to authenticate API calls.

**Q: What if I lose my phone?**
A: Your token is only on that device. If you're concerned, revoke the token on GitHub and generate a new one.

**Q: Can multiple people edit the same vault?**
A: Yes, but conflicts must be resolved manually (copy-paste after refresh).

**Q: Does this support all Obsidian features?**
A: No. It supports basic Markdown, `[[wiki links]]`, and `![[images]]`. No plugins, graph view, or advanced features.

**Q: Can I fork this and modify it?**
A: Yes! It's open source. Just be transparent with users about what your version does.

## License

MIT License - feel free to use, modify, and distribute.

## Contributing

Contributions welcome! Keep the spirit of simplicity:
- No build steps
- No frameworks
- Single HTML file
- Security-first mindset

## Support

Found a bug? Have a suggestion? Open an issue on GitHub.

---

**Remember**: Only use this app from trusted sources. Verify the code if you're security-conscious. Your GitHub token is powerful - protect it like a password.
