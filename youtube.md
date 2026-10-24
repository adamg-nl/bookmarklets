# YouTube

## Fix columns
```
javascript:(d=>{var s = d.createElement("style"); d.head.appendChild(s); s.innerText = "* { --ytd-rich-grid-items-per-row: 5 !important; }"})(document)
```

## Copy transcript

```
javascript:(function(){
  const selector = "ytd-transcript-segment-renderer:not([hidden]) .segment-text, transcript-segment-view-model .ytAttributedStringHost";
  
  function copyData() {
    const nodes = document.querySelectorAll(selector);
    if (!nodes.length) return false;

    const videoTitle = document.querySelector("#title").textContent.trim();
    const transcriptText = Array.from(nodes).map(n => n.textContent.trim()).filter(Boolean).join(' ');
    const url = window.location.origin + "/watch?v=" + new URL(window.location.href).searchParams.get('v');
    const fileContent = "# " + videoTitle + "\n\n" + url + "\n\n" + transcriptText + "\n";
    navigator.clipboard.writeText(fileContent);
    return true;
  }

  if (copyData()) return;

  const showBtn = document.querySelector('button[aria-label="Show transcript"], #primary-button button');
  if (showBtn) showBtn.click();

  const observer = new MutationObserver(() => {
    if (copyData()) observer.disconnect();
  });

  observer.observe(document.body, { childList: true, subtree: true });
})();
```

## Copy playlist URLs

Use on a playlist view page to get newline-separated URL in clipboard. 

```
javascript:(function(){
  /* Target video link elements within playlist items */
  const nodes = document.querySelectorAll('ytd-playlist-video-renderer a#video-title');
  
  if (!nodes.length) {
    return alert('No playlist videos found. Make sure you are on a YouTube playlist page.');
  }

  /* Extract hrefs, convert to clean URLs, and deduplicate */
  const urls = Array.from(nodes)
    .map(a => {
      const href = a.getAttribute('href');
      if (!href) return null;
      const url = new URL(href);
      const v = url.searchParams.get('v');
      return v ? `${window.location.origin}/watch?v=${v}` : null;
    })
    .filter(Boolean);

  const uniqueUrls = Array.from(new Set(urls));

  /* Copy newline-separated URLs to clipboard */
  navigator.clipboard.writeText(uniqueUrls.join('\n'));
  alert(`Copied ${uniqueUrls.length} video URL(s) to clipboard!`);
})();
```
