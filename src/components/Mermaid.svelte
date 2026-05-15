<script lang="ts">
  import { onMount } from "svelte";
  import mermaid from "mermaid";

  let observer: MutationObserver | null = null;

  function getTheme(): string {
    return document.documentElement.classList.contains("dark")
      ? "dark"
      : "default";
  }

  mermaid.initialize({
    startOnLoad: false,
    theme: getTheme(),
    securityLevel: "loose",
    fontFamily: "inherit",
  });

  async function renderMermaidDiagrams() {
    const codeBlocks = document.querySelectorAll<HTMLElement>(
      ".custom-md code.language-mermaid",
    );
    if (codeBlocks.length === 0) return;

    for (const codeBlock of codeBlocks) {
      if (codeBlock.hasAttribute("data-mermaid-rendered")) continue;
      codeBlock.setAttribute("data-mermaid-rendered", "");

      const pre = codeBlock.closest("pre");
      if (!pre) continue;

      const mermaidCode = codeBlock.textContent || "";
      if (!mermaidCode.trim()) continue;

      const id = `mermaid-${Math.random().toString(36).substring(2, 10)}`;

      try {
        const { svg } = await mermaid.render(id, mermaidCode);
        const wrapper = document.createElement("div");
        wrapper.className = "mermaid-diagram";
        wrapper.innerHTML = svg;
        pre.replaceWith(wrapper);
      } catch (error) {
        console.error("Mermaid render error:", error);
        const errorDiv = document.createElement("div");
        errorDiv.className =
          "mermaid-error text-sm text-red-500 dark:text-red-400 p-4 my-4 rounded-lg bg-red-50 dark:bg-red-900/20 border border-red-200 dark:border-red-800";
        errorDiv.textContent = `Mermaid render error: ${error}`;
        pre.replaceWith(errorDiv);
      }
    }
  }

  async function reRenderAll() {
    document.querySelectorAll(".mermaid-diagram").forEach((el) => {
      const pre = document.createElement("pre");
      const code = document.createElement("code");
      code.className = "language-mermaid";
      pre.appendChild(code);
      el.replaceWith(pre);
    });
    document.querySelectorAll(".mermaid-error").forEach((el) => el.remove());
    mermaid.initialize({ theme: getTheme() });
    await renderMermaidDiagrams();
  }

  onMount(async () => {
    await renderMermaidDiagrams();

    observer = new MutationObserver((mutations) => {
      for (const m of mutations) {
        if (m.attributeName === "class") {
          mermaid.initialize({ theme: getTheme() });
          reRenderAll();
        }
      }
    });
    observer.observe(document.documentElement, {
      attributes: true,
      attributeFilter: ["class"],
    });

    return () => observer?.disconnect();
  });
</script>

<div class="mermaid-container"></div>
