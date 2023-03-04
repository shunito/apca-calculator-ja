<script>
  import { onMount } from "svelte";
  import { calcAPCA, fontLookupAPCA } from "apca-w3";
  import { fontList } from "../conf/fonts.json";
  import { table as lcTable } from "../conf/apca-font2Lc-table.json";
  import FontWeightSample from "./FontWeightSample.svelte";

  const fontWeightList = [
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

  let fontSize = 18;
  let fontWeight = "400";
  let textColor = "#333333";
  let backgroundColor = "#eeeeee";
  let fontFaceClassName = fontList[0].className;

  let sampleText =
    "あのイーハトーヴォのすきとおった風、夏でも底に冷たさをもつ青いそら、うつくしい森で飾られたモリーオ市、郊外のぎらぎらひかる草の波。 またそのなかでいっしょになったたくさんのひとたち、ファゼーロとロザーロ、羊飼のミーロや、顔の赤いこどもたち、地主のテーモ、山猫博士のボーガント・デストゥパーゴなど、いまこの暗い巨きな石の建物のなかで考えていると、みんなむかし風のなつかしい青い幻燈のように思われます。";

  let contrastLc = calcAPCA(textColor, backgroundColor);
  let fcArray = fontLookupAPCA(contrastLc);

  let showLatinFontSample = false;
  let showFontMinus1Sample = false;
  let fontFeatureSettings = false;
  let hasFeatureSettings = true;

  let APCAresult = {};
  $: APCAresult;

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
    <h2>コントラスト シミュレーター</h2>
    <p>
      カラーを選択すると設定フォントサイズとウェイトでの表示例と、APCA基準値によるチェック結果が表示されます。<br
      />
      透明度なども考慮した基準値のシミュレーションはオリジナルの<a
        href="https://www.myndex.com/APCA/">APCA Contrast Calculator</a
      >を利用してください。
    </p>
  </div>

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
                <option value={weight} selected>{weight}</option>
              {:else}
                <option value={weight}>{weight}</option>
              {/if}
            {/each}
          </select>
        </div>
      </div>
    </div>
    <div>
      <fieldset class="inline">
        <label for="showLatinSample">
          <input
            type="checkbox"
            id="showLatinSample"
            name="showLatinSampleSwitch"
            role="switch"
            aria-checked="false"
            on:click={handleShowLatinSample}
          />
          ラテンフォント例を表示
        </label>
        <label for="showMinus1Sample">
          <input
            type="checkbox"
            id="showMinus1Sample"
            name="showMinus1SampleSwitch"
            role="switch"
            aria-checked="false"
            on:click={handleShowMinus1Sample}
          />
          指定の90%の例を表示
        </label>
        <label for="setFeatureSettings">
          <input
            type="checkbox"
            id="setFeatureSettings"
            name="setFeatureSettingsSwitch"
            role="switch"
            aria-checked="false"
            on:click={handleSetFeatureSettings}
          />
          文字詰め
        </label>
      </fieldset>
    </div>
  </form>

  <div class="resultTextArea" style:background-color={backgroundColor}>
    <p
      class={fontFaceClassName}
      style:font-size={`${fontSize}px`}
      style:font-weight={fontWeight}
      style:color={textColor}
      class:feature-settings-palt={fontFeatureSettings}
    >
      {sampleText}
    </p>
  </div>

  {#if showLatinFontSample}
    <div
      lang="en"
      class="resultTextArea"
      style:background-color={backgroundColor}
    >
      <p
        style="line-height: 1.6;"
        style:font-size={`${fontSize}px`}
        style:font-weight={fontWeight}
        style:color={textColor}
        class:feature-settings-palt={fontFeatureSettings}
      >
        <strong>Latin font</strong><br />
        Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor
        incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis
        nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.
        Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore
        eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident,
        sunt in culpa qui officia deserunt mollit anim id est laborum.
      </p>
    </div>
  {/if}

  {#if showFontMinus1Sample}
    <div class="resultTextArea" style:background-color={backgroundColor}>
      <p
        class={fontFaceClassName}
        style:font-size={`${fontSize * 0.9}px`}
        style:font-weight={fontWeight}
        style:color={textColor}
        class:feature-settings-palt={fontFeatureSettings}
      >
        <strong>フォントサイズ 90%</strong><br />
        {sampleText}
      </p>
    </div>
  {/if}

  <div class="result">
    <div class="headings">
      <h3>チェック結果</h3>
      <p>コントラストはLcの絶対値で評価されます。</p>
    </div>
    <ul>
      <li>
        APCAコントラスト <strong>{contrastLc.toFixed(1)}</strong>
        <small>Lc</small>
      </li>
      {#if APCAresult.Lc === "none"}<li>推奨されないフォント設定です。</li>
      {:else}
        <li>
          推奨コントラスト <strong>{APCAresult.Lc}</strong> <small>Lc</small>
          {#if Math.abs(contrastLc) >= APCAresult.Lc}（基準値クリア：Rating {APCAresult.Rating}
            ）{:else}（コントラスト不足）{/if}
        </li>
      {/if}
      {#if fontWeight === "100"}<li>
          フォントウェイト100は推奨されません。
        </li>{/if}
      {#if Math.abs(contrastLc) > APCAresult.Lc}
        {#if APCAresult.CopyrightOnly}<li>
            コピーライトや著者名を表示する場合のみ推奨できます。
          </li>{/if}
        {#if APCAresult.PreferredBlocksText}<li>
            本文での利用に推奨できます。
          </li>{/if}
        {#if APCAresult.Add15BlocksText}<li>
            本文で利用する場合には推奨コントラストに15追加した値を設定してください。
            （本文での推奨値：{Number(APCAresult.Lc) + 15} <small>Lc</small>）
          </li>
        {/if}
      {/if}
      {#if Math.abs(contrastLc) >= 90}<li>
          WCAG 2.1の達成基準 1.4.6 コントラスト (高度) 7:1（AAA）以上と同等。
        </li>
      {:else if Math.abs(contrastLc) >= 75}<li>
          WCAG 2.1の達成基準 1.4.3 コントラスト (最低限) 4.5:1（AA）以上と同等。
        </li>
      {:else if Math.abs(contrastLc) >= 60}<li>
          WCAG 2.1の達成基準 1.4.3 コントラスト (最低限)
          3:1（大きな文字の場合）（AA）以上と同等。
        </li>
      {/if}
    </ul>
    <p>
      評価についての詳しい説明は <a
        href="https://git.apcacontrast.com/documentation/APCA_in_a_Nutshell"
        >APCA in a Nutshell</a
      > を参照してください。
    </p>
  </div>

  <div class="result">
    <div class="headings">
      <h3>推奨値表示サンプル</h3>
      <p>
        計算されたAPCAのコントラスト値から、ウェイトに対する必要なフォントサイズの設定例です。
      </p>
    </div>

    <FontWeightSample
      weight={200}
      sampleFontSize={fcArray[1]}
      {textColor}
      {backgroundColor}
      {sampleText}
      {fontFaceClassName}
      {fontFeatureSettings}
    />
    <FontWeightSample
      weight={400}
      sampleFontSize={fcArray[3]}
      {textColor}
      {backgroundColor}
      {sampleText}
      {fontFaceClassName}
      {fontFeatureSettings}
    />
    <FontWeightSample
      weight={700}
      sampleFontSize={fcArray[6]}
      {textColor}
      {backgroundColor}
      {sampleText}
      {fontFaceClassName}
      {fontFeatureSettings}
    />
  </div>
</section>

<style>
  @layer component {
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
      margin: 2rem 0;
    }
    .result h3 {
      margin-bottom: 0rem;
    }
    .resultTextArea {
      padding: 1rem;
      line-height: 1.8;
      margin: 2px 0;
    }
    .resultTextArea p {
      margin: 0;
    }
    .inline {
      display: flex;
      flex-wrap: wrap;
    }
    .inline label {
      margin-right: 2rem;
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
