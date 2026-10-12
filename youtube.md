# YouTube

## Fix columns
```
javascript:(d=>{var s = d.createElement("style"); d.head.appendChild(s); s.innerText = "* { --ytd-rich-grid-items-per-row: 5 !important; }"})(document)
```

## Copy transcript

```
javascript:(function(){
  const selector = "ytd-engagement-panel-section-list-renderer[target-id='engagement-panel-searchable-transcript'][visibility='ENGAGEMENT_PANEL_VISIBILITY_EXPANDED'] ytd-transcript-segment-renderer yt-formatted-string";
  
  function copyData() {
    const titleEl = document.querySelector('ytd-watch-metadata #title h1 yt-formatted-string, h1.ytd-watch-metadata');
    const videoTitle = titleEl ? titleEl.innerText.trim() : 'Video Transcript';
    const nodes = document.querySelectorAll(selector);
    
    if (!nodes.length) return false;

    const transcriptText = Array.from(nodes).map(n => n.innerText.trim()).filter(Boolean).join(' ');
    navigator.clipboard.writeText(`# ${videoTitle}\n\n\n${transcriptText}`);
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
