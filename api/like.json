// api/like.js
// SASI LIKE LITE - Proxy

export default async function handler(req, res) {
  res.setHeader("Access-Control-Allow-Origin", "*");
  res.setHeader("Access-Control-Allow-Methods", "GET, POST, OPTIONS");
  res.setHeader("Access-Control-Allow-Headers", "Content-Type");

  if (req.method === "OPTIONS") {
    return res.status(200).end();
  }

  const { uid } = req.query;
  const server = "bd";

  if (!uid) {
    return res.status(400).json({ error: "UID is required", status: 0 });
  }

  if (!/^\d+$/.test(uid)) {
    return res.status(400).json({ error: "UID must be numeric", status: 0 });
  }

  const upstreamUrl = `https://sasi-like-lite.vercel.app/like?uid=${encodeURIComponent(uid)}&server_name=${encodeURIComponent(server)}`;

  try {
    const controller = new AbortController();
    const timeout = setTimeout(() => controller.abort(), 25000);

    const upstream = await fetch(upstreamUrl, {
      method: "GET",
      headers: {
        "Accept": "application/json",
        "User-Agent": "SASI-Like-Lite/1.0"
      },
      signal: controller.signal
    });

    clearTimeout(timeout);

    const data = await upstream.json().catch(() => ({}));

    if (!upstream.ok || data.status === 0) {
      return res.status(upstream.status || 500).json({
        error: data.error || "Upstream request failed",
        status: 0,
        ...data
      });
    }

    return res.status(200).json({
      LikesGivenByAPI: data.LikesGivenByAPI ?? 0,
      LikesafterCommand: data.LikesafterCommand ?? 0,
      LikesbeforeCommand: data.LikesbeforeCommand ?? 0,
      PlayerNickname: data.PlayerNickname || "Unknown",
      Region: data.Region || server.toUpperCase(),
      UID: data.UID || uid,
      dev: data.dev || "@TCP_UserBro",
      status: 1
    });

  } catch (err) {
    const message = err.name === "AbortError"
      ? "Upstream timeout"
      : err.message || "Network error";

    return res.status(500).json({ error: message, status: 0 });
  }
}
