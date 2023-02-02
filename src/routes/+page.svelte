<script lang="ts">
   let emoji = "\u2764";
   let copiedSVG = false;
   let copiedLink = false;
   let href = "";
   let raw = "";

   $: icon =
      typeof window !== "undefined" &&
      (window as any).twemoji.convert.toCodePoint(emoji.trim());
   $: src =
      "https://cdn.jsdelivr.net/gh/twitter/twemoji/assets/svg/" + icon + ".svg";
   $: {
      fetch(src)
         .then((r) => r.text())
         .then((r) => {
            raw = r;
            if (
               !r.match(/^<svg[^>]+xmlns="http\:\/\/www\.w3\.org\/2000\/svg"/)
            ) {
               r = r.replace(
                  /^<svg/,
                  '<svg xmlns="http://www.w3.org/2000/svg"'
               );
            }
            if (!r.match(/^<svg[^>]+"http\:\/\/www\.w3\.org\/1999\/xlink"/)) {
               r = r.replace(
                  /^<svg/,
                  '<svg xmlns:xlink="http://www.w3.org/1999/xlink"'
               );
            }

            r = '<?xml version="1.0" standalone="no"?>\r\n' + r;

            href = "data:image/svg+xml;charset=utf-8," + encodeURIComponent(r);
         });
   }

   function copySVG() {
      navigator.clipboard.writeText(raw);
      copiedSVG = true;
      setTimeout(() => {
         copiedSVG = false;
      }, 1000);
   }

   function copyLink() {
      navigator.clipboard.writeText(src);
      copiedLink = true;
      setTimeout(() => {
         copiedLink = false;
      }, 1000);
   }
</script>

<div class="page container py-5">
   <h1>Twemoji converter</h1>
   <p>Convert native emoji to twemoji link</p>

   <input type="text" class="form-control" bind:value={emoji} />

   <img class="d-block mt-4" {src} alt={emoji} />

   <div class="mt-4">
      <a {href} download={`twemoji-${emoji}.svg`} class="btn btn-primary"
         ><i class="bi bi-download" /> Download</a
      >
      <button
         class="btn btn-warning ms-2"
         disabled={copiedSVG}
         on:click={copySVG}
         ><i
            class={"bi " + (copiedSVG ? "bi-clipboard-check" : "bi-clipboard")}
         />
         {copiedSVG ? "Copied!" : "Copy SVG"}</button
      >
      <button
         class="btn btn-secondary ms-2"
         disabled={copiedLink}
         on:click={copyLink}
         ><i
            class={"bi " + (copiedLink ? "bi-clipboard-check" : "bi-clipboard")}
         />
         {copiedLink ? "Copied!" : "Copy link"}</button
      >
   </div>
</div>

<style>
   .page {
      max-width: 50rem;
   }

   .page img {
      height: 48px;
      width: 48px;
   }
</style>
