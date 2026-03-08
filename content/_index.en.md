---
title: "zhixian's site"
---

<div class="projects-grid">

<a href="https://clawpal.zhixian.io" target="_blank" class="project-card">
  <img src="/images/projects/clawpal.png" alt="ClawPal" class="project-logo">
  <div class="project-info">
    <h3 class="project-title">ClawPal</h3>
    <p class="project-desc">Desktop companion for OpenClaw</p>
  </div>
</a>

<a href="https://botdrop.app" target="_blank" class="project-card">
  <img src="/images/projects/botdrop.png" alt="BotDrop" class="project-logo">
  <div class="project-info">
    <h3 class="project-title">BotDrop</h3>
    <p class="project-desc">Turn Android into AI Agent server</p>
  </div>
</a>

<a href="https://owlia.bot" target="_blank" class="project-card">
  <img src="/images/projects/owliabot.png" alt="OwliaBot" class="project-logo">
  <div class="project-info">
    <h3 class="project-title">OwliaBot</h3>
    <p class="project-desc">Secure crypto-native AI Agent</p>
  </div>
</a>

<div class="podcast-card">
  <img src="/images/projects/podcast.png" alt="认知有县" class="podcast-logo nozoom">
  <div class="podcast-info">
    <h3 class="podcast-title">认知有县</h3>
    <div class="podcast-links">
      <a href="https://www.xiaoyuzhoufm.com/podcast/699d656b384af58740cef00d" target="_blank" title="小宇宙">
        <img src="/images/projects/xiaoyuzhou.png" alt="小宇宙" class="nozoom">
      </a>
      <a href="https://open.spotify.com/show/34rYkRrG5UzceVS6Sxet1l" target="_blank" title="Spotify">
        <img src="/images/projects/spotify.svg" alt="Spotify" class="nozoom">
      </a>
      <a href="https://apple.co/4ay0hDL" target="_blank" title="Apple Podcasts">
        <img src="/images/projects/apple-podcasts.svg" alt="Apple Podcasts" class="nozoom">
      </a>
    </div>
  </div>
</div>

<div class="project-card wechat-trigger" onclick="document.getElementById('wechat-modal').classList.remove('hidden')" style="cursor: pointer;">
  <div class="project-logo wechat-icon">
    <svg viewBox="0 0 24 24" fill="currentColor" style="width: 100%; height: 100%; color: #07C160;">
      <path d="M8.691 2.188C3.891 2.188 0 5.476 0 9.53c0 2.212 1.17 4.203 3.002 5.55a.59.59 0 0 1 .213.665l-.39 1.48c-.019.07-.048.141-.048.213 0 .163.13.295.29.295a.326.326 0 0 0 .167-.054l1.903-1.114a.864.864 0 0 1 .717-.098 10.16 10.16 0 0 0 2.837.403c.276 0 .543-.027.811-.05-.857-2.578.157-4.972 1.932-6.446 1.703-1.415 3.882-1.98 5.853-1.838-.576-3.583-4.196-6.348-8.596-6.348zM5.785 5.991c.642 0 1.162.529 1.162 1.18a1.17 1.17 0 0 1-1.162 1.178A1.17 1.17 0 0 1 4.623 7.17c0-.651.52-1.18 1.162-1.18zm5.813 0c.642 0 1.162.529 1.162 1.18a1.17 1.17 0 0 1-1.162 1.178 1.17 1.17 0 0 1-1.162-1.178c0-.651.52-1.18 1.162-1.18zm5.34 2.867c-1.797-.052-3.746.512-5.28 1.786-1.72 1.428-2.687 3.72-1.78 6.22.942 2.453 3.666 4.229 6.884 4.229.826 0 1.622-.12 2.361-.336a.722.722 0 0 1 .598.082l1.584.926a.272.272 0 0 0 .14.047c.134 0 .24-.111.24-.247 0-.06-.023-.12-.038-.177l-.327-1.233a.582.582 0 0 1-.023-.156.49.49 0 0 1 .201-.398C23.024 18.48 24 16.82 24 14.98c0-3.21-2.931-5.837-6.656-6.088v-.001c-.135-.01-.27-.027-.406-.033zm-2.53 3.274c.535 0 .969.44.969.982a.976.976 0 0 1-.969.983.976.976 0 0 1-.969-.983c0-.542.434-.982.97-.982zm4.844 0c.535 0 .969.44.969.982a.976.976 0 0 1-.969.983.976.976 0 0 1-.969-.983c0-.542.434-.982.969-.982z"/>
    </svg>
  </div>
  <div class="project-info">
    <h3 class="project-title">WeChat</h3>
    <p class="project-desc">知县的衙门</p>
  </div>
</div>

</div>

<!-- WeChat QR Modal -->
<div id="wechat-modal" class="hidden" style="position: fixed; inset: 0; z-index: 50; display: flex; align-items: center; justify-content: center; background: rgba(0,0,0,0.5);" onclick="if(event.target===this)this.classList.add('hidden')">
  <div style="background: white; border-radius: 1rem; padding: 1.5rem; margin: 1rem; max-width: 20rem; text-align: center; box-shadow: 0 25px 50px -12px rgba(0,0,0,0.25);">
    <h3 style="font-size: 1.25rem; font-weight: bold; margin-bottom: 1rem;">Scan to follow</h3>
    <img src="/images/wechat-qr.jpg" alt="知县的衙门" style="width: 16rem; height: 16rem; margin: 0 auto; border-radius: 0.75rem;">
    <p style="margin-top: 1rem; color: #666;">知县的衙门</p>
    <button onclick="document.getElementById('wechat-modal').classList.add('hidden')" style="margin-top: 1rem; padding: 0.5rem 1.5rem; border-radius: 0.5rem; background: #eee; border: none; cursor: pointer;">Close</button>
  </div>
</div>
