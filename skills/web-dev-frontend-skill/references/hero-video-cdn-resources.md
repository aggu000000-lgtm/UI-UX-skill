# Hero Video Background CDN Resources

> **2026 STANDARDS**: Use these CDN-hosted videos for hero sections. All sources are royalty-free, commercially usable, and optimized for web performance.

---

## VERIFIED WORKING URLs (Tested 2026-04-12)

### Mixkit CDN (Recommended - No Attribution Required)

**Base URL**: `https://assets.mixkit.co/videos/[ID]/[ID]-720.mp4`

**VERIFIED WORKING:**
- `https://assets.mixkit.co/videos/47761/47761-720.mp4` - Aerial Ocean Waves
- `https://assets.mixkit.co/videos/4633/4633-720.mp4` - Clouds and Blue Sky
- `https://assets.mixkit.co/videos/51500/51500-720.mp4` - City Night Lights
- `https://assets.mixkit.co/videos/9301/9301-720.mp4` - Technology Screen
- `https://assets.mixkit.co/videos/2168/2168-720.mp4` - Traffic at Night

**URL Pattern**: `https://assets.mixkit.co/videos/[VIDEO_ID]/[VIDEO_ID]-720.mp4`

---

## HOW TO FIND MORE WORKING URLs

### Method 1: Browse Mixkit and Extract URL
1. Go to https://mixkit.co/free-stock-video/
2. Click any video you want
3. Open browser DevTools → Network tab
4. Play the video
5. Find the `.mp4` request in Network tab
6. Copy the URL (format: `https://assets.mixkit.co/videos/[ID]/[ID]-720.mp4`)

### Method 2: Direct from Page Source
1. Right-click video page → View Source
2. Search for `assets.mixkit.co/videos/`
3. Extract URL pattern

---

## PEXELS CDN (Free for Commercial Use)

**Pexels requires proper Referer header.** Direct embedding may not work without referrer.

**To use Pexels:**
1. Get video URL from https://pexels.com/search/videos/
2. Use Pexels API to get direct download links
3. Or use a CDN proxy with proper headers

**API Example:**
```bash
curl -H "Authorization: YOUR_API_KEY" \
  "https://api.pexels.com/videos/videos/{id}/download"
```

---

## COVERR CDN (Free API Available)

**Base URL**: `https://storage.coverr.co`

Coverr provides a free API with direct CDN links:
- Sign up at https://coverr.co/developers
- API provides signed URLs for direct video access
- No attribution required

---

## PRO PATTERNS FOR 2026

### Optimal Video Implementation

```html
<video 
  autoplay 
  loop 
  muted 
  playsinline 
  poster="fallback-image.jpg"
  preload="metadata"
>
  <source src="video-720p.mp4" type="video/mp4" media="(max-width: 720px)">
  <source src="video-1080p.mp4" type="video/mp4">
  <source src="video-1080p.webm" type="video/webm">
</video>
```

### CSS Hero Video Background

```css
.hero-video-bg {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
  z-index: -1;
}

.hero-overlay {
  position: absolute;
  inset: 0;
  background: linear-gradient(
    to bottom,
    rgba(15, 23, 42, 0.6) 0%,
    rgba(15, 23, 42, 0.3) 50%,
    rgba(15, 23, 42, 0.8) 100%
  );
}
```

### Performance Checklist

- [ ] Keep videos under 8MB for hero sections
- [ ] Use 720p for mobile, 1080p for desktop
- [ ] Always provide WebM + MP4 formats
- [ ] Include poster image for immediate display
- [ ] Use `preload="metadata"` to avoid blocking
- [ ] Respect `prefers-reduced-motion` — swap to static image

### Accessibility

```css
@media (prefers-reduced-motion: reduce) {
  .hero-video-bg {
    display: none;
  }
  .hero-fallback {
    display: block;
  }
}
```

---

## VIDEO SOURCES COMPARISON

| Source | Cost | Attribution | API | Direct Embed | License |
|--------|------|------------|-----|-------------|---------|
| Mixkit | Free | Not Required | No | ✅ Yes | Commercial OK |
| Pexels | Free | Not Required | Yes | ⚠️ Limited | Commercial OK |
| Coverr | Free | Not Required | Yes | ✅ Yes | Commercial OK |
| Videvo | Free | Sometimes | No | ✅ Yes | Varies |

---

## RECOMMENDED HERO VIDEO CATEGORIES

1. **SaaS/Tech**: Abstract digital, network visuals, screen recordings
2. **Nature/Wellness**: Oceans, forests, sunrises, calm water
3. **Business**: City skylines, teamwork, professional settings
4. **Creative/Portfolio**: Artistic, dramatic, unique perspectives
5. **Dark Mode**: Night city, dramatic skies, moody lighting

---

## ATTRIBUTION TEMPLATES

### Mixkit
```
Video by Mixkit (mixkit.co)
License: Mixkit Free License
```

### Pexels
```
Video by [Creator Name] on Pexels (pexels.com)
License: Pexels License (Free for commercial use)
```

---

**Last Updated**: 2026-04-12
**Note**: Mixkit URL patterns may change. Always verify URLs before production use.
