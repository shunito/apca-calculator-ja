<script>
  export let textColor;
  export let backgroundColor;
  export let sampleText;
  export let fontFaceClassName;
  export let sampleFontSize;
  export let weight;
</script>

<section class="grid sample-setting">
  <p>Weight: {weight} <br /> Font size: {sampleFontSize}px</p>
  <!-- svelte-ignore a11y-no-noninteractive-tabindex -->
  <div
    class="sample-box"
    style:background-color={backgroundColor}
    tabindex="0"
  >
    <p
      class={fontFaceClassName}
      style:font-size={`${sampleFontSize}px`}
      style:font-weight={weight}
      style:color={textColor}
    >
      {sampleText}
    </p>
  </div>
</section>

<style>
  .sample-setting {
    grid-template-columns: 1fr 5fr;
  }
  .sample-setting p {
    margin: 0;
  }

  .sample-box {
    padding:1rem;
    line-height:1.8;
    max-height:10rem; overflow:scroll;
  }
</style>
