<script>
  import { onMount } from "svelte";
  import { calcAPCA, fontLookupAPCA } from "apca-w3";
  import { fontList } from "../conf/fonts.json";
  import { table as lcTable } from "../conf/apca-font2Lc-table.json";
  import blinder from "color-blind";

  let fontSize = 18;
  let fontWeight = "400";
  let textColor = "#333333";
  let backgroundColor = "#eeeeee";
  let fontFaceClassName = fontList[0].className;

  let contrastLc = calcAPCA(textColor, backgroundColor);
  let fcArray = fontLookupAPCA(contrastLc);

  let showLatinFontSample = false;
  let showFontMinus1Sample = false;
  let fontFeatureSettings = false;
  let hasFeatureSettings = true;

  let APCAresult = {};
  $: APCAresult;

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
    hasFeatureSettings = fontList[value].hasFeatureSettings;

    const checkbox = document.getElementById("setFeatureSettings");
    checkbox.checked = false;
    fontFeatureSettings = false;

    if (hasFeatureSettings) {
      checkbox.disabled = false;
    } else {
      checkbox.disabled = true;
    }
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

  const handleShowLatinSample = (event) => {
    const checkbox = event.target;
    showLatinFontSample = checkbox.checked;
    checkbox.setAttribute("aria-checked", checkbox.checked);
  };

  const handleShowMinus1Sample = (event) => {
    const checkbox = event.target;
    showFontMinus1Sample = checkbox.checked;
    checkbox.setAttribute("aria-checked", checkbox.checked);
  };

  const handleSetFeatureSettings = (event) => {
    const checkbox = event.target;
    fontFeatureSettings = checkbox.checked;
    checkbox.setAttribute("aria-checked", checkbox.checked);
  };

  onMount(() => {
    calculator();
  });
</script>

<section>
  <div class="headings">
    <h2>色覚特性ごとのコントラスト比較</h2>
    <p>
      二つの色を選択すると、色覚特性ごとのシミュレーション結果とコントラスト（Lc）が表示されます。
    </p>
  </div>

  <form on:submit|preventDefault={handleSubmit}>
    <div class="grid color-selector">
      <div>
        <label for="textColor">カラー(1)</label>
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
      <div>
        <label for="backgroundColor">カラー(2)</label>
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
  </form>

  <div class="result">
    <h3>C型色覚</h3>
    <div class="resultTextArea">
      <div class="resultOneBox">
        <p class="resultLc">
          Lc : {calcAPCA(textColor, backgroundColor).toFixed(1)}
        </p>
        <p
          class={fontFaceClassName}
          style:font-size={`${fontSize}px`}
          style:font-weight={fontWeight}
          style:color={textColor}
          style:background-color={backgroundColor}
          class:feature-settings-palt={fontFeatureSettings}
        >
          あいうえおabc
        </p>
      </div>
      <div class="resultOneBox">
        <p class="resultLc">
          Lc : {calcAPCA(backgroundColor, textColor).toFixed(1)}
        </p>
        <p
          class={fontFaceClassName}
          style:font-size={`${fontSize}px`}
          style:font-weight={fontWeight}
          style:color={backgroundColor}
          style:background-color={textColor}
          class:feature-settings-palt={fontFeatureSettings}
        >
          あいうえおabc
        </p>
      </div>
      <div class="blinderColor">
        <p>
          カラー（１）：{textColor} <br />
          カラー（２）：{backgroundColor} <br />
        </p>
      </div>
    </div>
  </div>

  <div class="result">
    <h3>P型色覚（Protanopia）</h3>
    <div class="resultTextArea">
      <div class="resultOneBox">
        <p class="resultLc">
          Lc : {calcAPCA(
            blinder.protanopia(textColor),
            blinder.protanopia(backgroundColor)
          ).toFixed(1)}
        </p>
        <p
          class={fontFaceClassName}
          style:font-size={`${fontSize}px`}
          style:font-weight={fontWeight}
          style:color={blinder.protanopia(textColor)}
          style:background-color={blinder.protanopia(backgroundColor)}
          class:feature-settings-palt={fontFeatureSettings}
        >
          あいうえおabc
        </p>
      </div>
      <div class="resultOneBox">
        <p class="resultLc">
          Lc : {calcAPCA(
            blinder.protanopia(backgroundColor),
            blinder.protanopia(textColor)
          ).toFixed(1)}
        </p>
        <p
          class={fontFaceClassName}
          style:font-size={`${fontSize}px`}
          style:font-weight={fontWeight}
          style:color={blinder.protanopia(backgroundColor)}
          style:background-color={blinder.protanopia(textColor)}
          class:feature-settings-palt={fontFeatureSettings}
        >
          あいうえおabc
        </p>
      </div>
      <div class="blinderColor">
        <p>
          カラー（１）：{blinder.protanopia(textColor)} <br />
          カラー（２）：{blinder.protanopia(backgroundColor)} <br />
        </p>
      </div>
    </div>
  </div>

  <div class="result">
    <h3>D型色覚（Deuteranopia）</h3>
    <div class="resultTextArea">
      <div class="resultOneBox">
        <p class="resultLc">
          Lc : {calcAPCA(
            blinder.deuteranopia(textColor),
            blinder.deuteranopia(backgroundColor)
          ).toFixed(1)}
        </p>
        <p
          class={fontFaceClassName}
          style:font-size={`${fontSize}px`}
          style:font-weight={fontWeight}
          style:color={blinder.deuteranopia(textColor)}
          style:background-color={blinder.deuteranopia(backgroundColor)}
          class:feature-settings-palt={fontFeatureSettings}
        >
          あいうえおabc
        </p>
      </div>
      <div class="resultOneBox">
        <p class="resultLc">
          Lc : {calcAPCA(
            blinder.deuteranopia(backgroundColor),
            blinder.deuteranopia(textColor)
          ).toFixed(1)}
        </p>
        <p
          class={fontFaceClassName}
          style:font-size={`${fontSize}px`}
          style:font-weight={fontWeight}
          style:color={blinder.deuteranopia(backgroundColor)}
          style:background-color={blinder.deuteranopia(textColor)}
          class:feature-settings-palt={fontFeatureSettings}
        >
          あいうえおabc
        </p>
      </div>
      <div class="blinderColor">
        <p>
          カラー（１）：{blinder.deuteranopia(textColor)} <br />
          カラー（２）：{blinder.deuteranopia(backgroundColor)} <br />
        </p>
      </div>
    </div>
  </div>

  <div class="result">
    <h3>T型色覚（Tritanopia）</h3>
    <div class="resultTextArea">
      <div class="resultOneBox">
        <p class="resultLc">
          Lc : {calcAPCA(
            blinder.tritanopia(textColor),
            blinder.tritanopia(backgroundColor)
          ).toFixed(1)}
        </p>
        <p
          class={fontFaceClassName}
          style:font-size={`${fontSize}px`}
          style:font-weight={fontWeight}
          style:color={blinder.tritanopia(textColor)}
          style:background-color={blinder.tritanopia(backgroundColor)}
          class:feature-settings-palt={fontFeatureSettings}
        >
          あいうえおabc
        </p>
      </div>
      <div class="resultOneBox">
        <p class="resultLc">
          Lc : {calcAPCA(
            blinder.tritanopia(backgroundColor),
            blinder.tritanopia(textColor)
          ).toFixed(1)}
        </p>
        <p
          class={fontFaceClassName}
          style:font-size={`${fontSize}px`}
          style:font-weight={fontWeight}
          style:color={blinder.tritanopia(backgroundColor)}
          style:background-color={blinder.tritanopia(textColor)}
          class:feature-settings-palt={fontFeatureSettings}
        >
          あいうえおabc
        </p>
      </div>
      <div class="blinderColor">
        <p>
          カラー（１）：{blinder.tritanopia(textColor)} <br />
          カラー（２）：{blinder.tritanopia(backgroundColor)} <br />
        </p>
      </div>
    </div>
  </div>

  <div class="result">
    <h3>A型色覚（Achromatopsia）</h3>
    <div class="resultTextArea">
      <div class="resultOneBox">
        <p class="resultLc">
          Lc : {calcAPCA(
            blinder.achromatopsia(textColor),
            blinder.achromatopsia(backgroundColor)
          ).toFixed(1)}
        </p>
        <p
          class={fontFaceClassName}
          style:font-size={`${fontSize}px`}
          style:font-weight={fontWeight}
          style:color={blinder.achromatopsia(textColor)}
          style:background-color={blinder.achromatopsia(backgroundColor)}
          class:feature-settings-palt={fontFeatureSettings}
        >
          あいうえおabc
        </p>
      </div>
      <div class="resultOneBox">
        <p class="resultLc">
          Lc : {calcAPCA(
            blinder.achromatopsia(backgroundColor),
            blinder.achromatopsia(textColor)
          ).toFixed(1)}
        </p>
        <p
          class={fontFaceClassName}
          style:font-size={`${fontSize}px`}
          style:font-weight={fontWeight}
          style:color={blinder.achromatopsia(backgroundColor)}
          style:background-color={blinder.achromatopsia(textColor)}
          class:feature-settings-palt={fontFeatureSettings}
        >
          あいうえおabc
        </p>
      </div>
      <div class="blinderColor">
        <p>
          カラー（１）：{blinder.achromatopsia(textColor)} <br />
          カラー（２）：{blinder.achromatopsia(backgroundColor)} <br />
        </p>
      </div>
    </div>
  </div>
</section>

<style>
  @layer component {
    .color-selector {
      grid-template-columns: auto auto;
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
    .result h3 {
      margin: 0;
    }
    .resultTextArea {
      display: flex;
      padding: 0.4rem;
      line-height: 1.8;
      margin: 2px 0;
    }
    .resultTextArea p {
      margin: 0;
      padding: 1rem;
    }
    .resultOneBox {
      display: flex;
      border: 1px solid #ccc;
      margin: 2px;
    }
    .resultLc {
      width: 8rem;
      text-align: center;
      white-space: nowrap;
    }
    .blinderColor p {
      font-size: 0.8rem;
      padding: 0.5rem;
    }

    @media (max-width: 576px) {
      .color-selector {
        grid-template-columns: 1fr;
      }
      .font-selector {
        grid-template-columns: 3fr 2fr;
      }
    }
  }
</style>
