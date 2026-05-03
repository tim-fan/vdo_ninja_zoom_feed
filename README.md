# VDO.ninja Pan/Zoom Viewer

A specialized WebRTC stream wrapper for VDO.ninja designed for high-precision inspection. This viewer bypasses standard UI constraints to provide hardware-accelerated pan and zoom capabilities, making it ideal for monitoring systems, remote technical assistance, or any use case requiring digital magnification of a live video feed.

## Live Access
Viewer URL:
https://[YOUR-GITHUB-USERNAME].github.io/[YOUR-REPO-NAME]/viewer.html

## Usage & URL Parameters
This viewer acts as a transparent proxy. It captures parameters from its own URL and injects them into the VDO.ninja iframe along with several performance-optimizing overrides.

Basic usage — append your VDO.ninja view ID and optional room name to the viewer URL:

`.../viewer.html?view=[STREAM_ID]&room=[ROOM_NAME]`

### Controls
- Mobile: pinch-to-zoom and single-finger drag to pan.
- Desktop: mouse wheel to zoom at the cursor position, click-and-drag to pan.

## Maximizing Resolution
WebRTC engines often prioritize framerate over spatial resolution. To preserve pixel density for digital zooming, apply these configurations.

1. Sender Side (the camera)
- `&contenthint=detail` — prioritize spatial detail; the engine will drop framerate before reducing resolution.
- `&quality=0` — request the highest hardware profile (often 1080p).
- `&noscale` — prevent internal downscaling.

Example sender URL:
`https://vdo.ninja/?push=[ID]&contenthint=detail&quality=0`

2. Viewer Side (this tool)
This viewer injects the following into the iframe:
- `&scale=100` — request 100% of source resolution regardless of iframe size.
- `&videobitrate=15000` — request a high bitrate ceiling to reduce compression artifacts.

## Technical Architecture
- Logic: uses the URLSearchParams API to bridge session parameters between the parent and the VDO.ninja guest iframe.
- Rendering: uses 3D CSS transforms (`scale3d`, `translate3d`) via the `@panzoom/panzoom` library so GPU-accelerated interactions remain smooth at 60fps.
- Security: requires a secure context (HTTPS). VDO.ninja uses the browser's Web Crypto API (`crypto.subtle`) which will not initialize on insecure origins or file:// pages.
- Autoplay policy: provides a user-interaction overlay to satisfy browser autoplay restrictions so audio/video can start after the first user gesture.

## Limitations
- Digital vs. optical: this tool performs software-based digital zoom. Clarity depends on sender upload bitrate and sensor quality.
- Thermal throttling: sustained high-bitrate 1080p encoding on mobile senders may cause thermal throttling and reduced performance.