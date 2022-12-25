<script>
  import { onMount } from "svelte";
  import { calcAPCA, fontLookupAPCA } from "apca-w3";
  import { fontList } from "../conf/fonts.json";
  import { table as lcTable } from "../conf/apca-font2Lc-table.json";

  export const fontWeightList = [
    "100",
    "200",
    "300",
    "400",
    "500",
    "600",
    "700",
    "800",
    "900",
  ];

  export let textColor = "#333333";
  export let backgroundColor = "#eeeeee";
  export let sampleText =
    "あのイーハトーヴォのすきとおった風、夏でも底に冷たさをもつ青いそら、うつくしい森で飾られたモリーオ市、郊外のぎらぎらひかる草の波。 またそのなかでいっしょになったたくさんのひとたち、ファゼーロとロザーロ、羊飼のミーロや、顔の赤いこどもたち、地主のテーモ、山猫博士のボーガント・デストゥパーゴなど、いまこの暗い巨きな石の建物のなかで考えていると、みんなむかし風のなつかしい青い幻燈のように思われます。";

  export let contrastLc = calcAPCA(textColor, backgroundColor);
  export let fcArray = fontLookupAPCA(contrastLc);

  export let fontFace = fontList[0].name.replaceAll("+", " ");
  export let fontFaceClassName = fontList[0].className;

  export let fontSize = 18;
  export let fontWeight = "400";

  export let APCAresult = {};

  let resultBlock = null;
  $: APCAresult;
  $: resultBlock;

  // TODO: この辺り初期設計がいけてないのでリファクタ必要
  // JSONの構造と合わせて変えたい
  const getAPCAresult = (list) => {
    const num = parseInt(fontWeight) / 100 - 1;
    return list[num][fontWeight];
  };

  const calculator = () => {
    const result = lcTable.filter((value) => {
      return value.px === fontSize;
    });
    if (result.length > 0) {
      APCAresult = getAPCAresult(result[0].weight);
      contrastLc = calcAPCA(textColor, backgroundColor);
      fcArray = fontLookupAPCA(contrastLc);
    }
  };

  const handleColorChange = (event) => {
    calculator();
  };

  const handleSwapColor = (event) => {
    const swap = textColor;
    textColor = backgroundColor;
    backgroundColor = swap;
    calculator();
  };

  const handleFontFaceChange = (event) => {
    const value = event.target.value;
    fontFaceClassName = fontList[value].className;
    fontFace = fontList[value].name.replaceAll("+", " ");

    resultBlock.classList = [];
    resultBlock.classList.add(fontFaceClassName);
  };

  const handleFontSizeChange = (event) => {
    const value = event.target.value;
    const fontType = lcTable[value];
    fontSize = fontType.px;
    calculator();
  };

  const handleFontWeightChange = (event) => {
    fontWeight = event.target.value;
    calculator();
  };

  const handleChangeSampleText = (event) => {
    sampleText = event.target.value;
  };

  const handleSubmit = () => {
    calculator();
  };

  onMount(() => {
    calculator();
  });
</script>

<section>
  <h2>コントラスト シミュレーター</h2>

  <form on:submit|preventDefault={handleSubmit}>
    <div class="grid color-selector">
      <div>
        <label for="textColor">テキストカラー</label>
        <div class="grid color-set">
          <input
            type="text"
            id="textColor"
            name="textColor"
            bind:value={textColor}
            on:blur={handleColorChange}
          />
          <input
            type="color"
            id="textColorPicker"
            name="color"
            bind:value={textColor}
            aria-label="テキスト カラーピッカー"
            class="color-picker"
            on:change={handleColorChange}
          />
        </div>
      </div>
      <button
        type="button"
        class="btn-swap secondary"
        on:click={handleSwapColor}>入替</button
      >
      <div>
        <label for="backgroundColor">バックグラウンドカラー</label>
        <div class="grid color-set">
          <input
            type="text"
            id="backgroundColor"
            name="textColor"
            bind:value={backgroundColor}
            on:blur={handleColorChange}
          />
          <input
            type="color"
            id="backgroundColorPicker"
            name="color"
            bind:value={backgroundColor}
            aria-label="バックグラウンド カラーピッカー"
            class="color-picker"
            on:change={handleColorChange}
          />
        </div>
      </div>
    </div>
    <div>
      <label for="sampleText">サンプル文章</label>
      <textarea
        id="sampleText"
        class="sampleTextArea"
        on:change={handleChangeSampleText}>{sampleText}</textarea
      >
    </div>
    <div class="grid font-selector">
      <div>
        <label for="fontType">表示フォント</label>
        <select id="fontType" on:change={handleFontFaceChange}>
          {#each fontList as font, index}
            <option value={index}>{font.name.replaceAll("+", " ")}</option>
          {/each}
        </select>
      </div>
      <div>
        <label for="fontSize">フォントサイズ</label>
        <select id="fontSize" on:change={handleFontSizeChange}>
          {#each lcTable as font, index}
            {#if font.px === fontSize}
              <option value={index} selected>{font.px}px</option>
            {:else}
              <option value={index}>{font.px}px</option>
            {/if}
          {/each}
        </select>
      </div>
      <div>
        <label for="fontWeight">ウェイト</label>
        <div class="grid">
          <select id="fontWeight" on:change={handleFontWeightChange}>
            {#each fontWeightList as weight, index}
              {#if weight === fontWeight}
                <option value={weight} selected>{weight}px</option>
              {:else}
                <option value={weight}>{weight}px</option>
              {/if}
            {/each}
          </select>
        </div>
      </div>
    </div>
  </form>

  <div
    style:font-size={`${fontSize}px`}
    style:font-weight={fontWeight}
    style:color={textColor}
    style:background-color={backgroundColor}
    style="padding:1rem; line-height:1.8;"
    bind:this={resultBlock}
  >
    {sampleText}
  </div>
  <div class="result">
    <h3>チェック結果</h3>
    <ul>
      <li>
        APCAコントラスト <strong>{contrastLc.toFixed(1)}</strong>
        <small>Lc</small>
      </li>
      {#if APCAresult.Lc == "none"}<li>推奨されないフォント設定です。</li>
      {:else}
        <li>
          推奨コントラスト <strong>{APCAresult.Lc}</strong> <small>Lc</small>
          {#if Math.abs(contrastLc) > APCAresult.Lc}（基準値クリア：Rating {APCAresult.Rating}
            ）{:else}（コントラスト不足）{/if}
        </li>
      {/if}
      {#if fontWeight == "100"}<li>
          フォントウェイト100は推奨されません。
        </li>{/if}
      {#if APCAresult.CopyrightOnly}<li>
          コピーライトや著者名を表示する場合のみ推奨できます。
        </li>{/if}
      {#if APCAresult.PreferredBlocksText}<li>
          本文での利用に推奨できます。
        </li>{/if}
      {#if APCAresult.Add15BlocksText}<li>
          本文で利用する場合には推奨コントラストに15追加した値を設定してください。
          （本文での推奨値：{Number(APCAresult.Lc) + 15}）
        </li>{/if}
      {#if Math.abs(contrastLc) > 90}<li>WCAG 2の基準 7:1以上と同等。</li>
      {:else if Math.abs(contrastLc) > 75}<li>
          WCAG 2の基準 4.5:1以上と同等。
        </li>
      {:else if Math.abs(contrastLc) > 60}<li>
          WCAG 2の基準 3:1以上と同等。
        </li>{/if}
    </ul>
  </div>
</section>

<style>
  .color-selector {
    grid-template-columns: auto 5rem auto;
    align-items: end;
  }
  .color-set {
    grid-template-columns: 3fr 1fr;
  }
  .font-selector {
    grid-template-columns: 2fr 1fr 1fr;
  }
  .btn-swap {
    white-space: nowrap;
    max-height: 3rem;
  }
  .sampleTextArea {
    height: 6rem;
    font-size: 0.8rem;
  }
  .result {
    margin: 2rem 1rem;
  }
</style>
