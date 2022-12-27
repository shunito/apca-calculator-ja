<script>
    export let textColor;
    export let backgroundColor;
    export let sampleText;
    export let fontFace;
    export let sampleFontSize;
    export let weight;
</script>

<section class="grid sample-setting">
    <p>Weight: {weight} <br> Font size: {sampleFontSize}px</p>
    <div
        style="padding:1rem; line-height:1.8; max-height:10rem; overflow:scroll;"
        style:background-color={backgroundColor}>
        <p
            class={fontFace}
            style:font-size={`${sampleFontSize}px`}
            style:font-weight={weight}
            style:color={textColor}
        >{sampleText}</p>
    </div>
</section>

<style>
  .sample-setting {
    grid-template-columns: 1fr 5fr;
  }

</style>