<script lang="ts">
  import { onMount } from "svelte";

  let {
    repo,
    repoId,
    category,
    categoryId,
    mapping = "pathname",
    reactionsEnabled = "1",
    emitMetadata = "0",
    inputPosition = "top",
    lang = "zh_CN",
    loading = "lazy",
  }: {
    repo: string;
    repoId: string;
    category: string;
    categoryId: string;
    mapping?: string;
    reactionsEnabled?: string;
    emitMetadata?: string;
    inputPosition?: string;
    lang?: string;
    loading?: string;
  } = $props();

  let containerEl: HTMLDivElement;
  let scriptEl: HTMLScriptElement | null = null;
  let observer: MutationObserver | null = null;

  function getTheme(): string {
    return document.documentElement.classList.contains("dark")
      ? "dark"
      : "light";
  }

  function updateGiscusTheme() {
    const iframe = document.querySelector<HTMLIFrameElement>(
      "iframe.giscus-frame",
    );
    if (iframe?.contentWindow) {
      iframe.contentWindow.postMessage(
        { giscus: { setConfig: { theme: getTheme() } } },
        "https://giscus.app",
      );
    }
  }

  function injectScript() {
    if (scriptEl) return;
    const script = document.createElement("script");
    script.src = "https://giscus.app/client.js";
    script.setAttribute("data-repo", repo);
    script.setAttribute("data-repo-id", repoId);
    script.setAttribute("data-category", category);
    script.setAttribute("data-category-id", categoryId);
    script.setAttribute("data-mapping", mapping);
    script.setAttribute("data-reactions-enabled", reactionsEnabled);
    script.setAttribute("data-emit-metadata", emitMetadata);
    script.setAttribute("data-input-position", inputPosition);
    script.setAttribute("data-theme", getTheme());
    script.setAttribute("data-lang", lang);
    script.setAttribute("data-loading", loading);
    script.crossOrigin = "anonymous";
    script.async = true;
    containerEl.appendChild(script);
    scriptEl = script;
  }

  onMount(() => {
    injectScript();

    observer = new MutationObserver((mutations) => {
      for (const m of mutations) {
        if (m.attributeName === "class") {
          updateGiscusTheme();
        }
      }
    });
    observer.observe(document.documentElement, {
      attributes: true,
      attributeFilter: ["class"],
    });

    return () => {
      observer?.disconnect();
      if (scriptEl) {
        scriptEl.remove();
        scriptEl = null;
      }
    };
  });
</script>

<div bind:this={containerEl} class="giscus-container mt-8">
  <!-- Giscus will inject here -->
</div>
