<!DOCTYPE html>
<html lang="zh-Hant">
<head>
<meta charset="UTF-8">
<title>風電廠商投資儲能效益評估</title>
<link href="https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@400;700&display=swap" rel="stylesheet">
<style>
  body { font-family: 'Noto Sans TC', sans-serif; background-color: #f4f4f9; color: #333; padding: 20px; }


.header {
    position: relative; /* 確保可以正確放置偽元素 */
    color: black; /* 文字顏色 */
    font-weight: bold; /* 加粗 */
    padding: 20px;
    text-align: left;
    font-size: 24px;
}

.header::after {
    content: ""; /* 必須設置，即使沒有實際內容 */
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background-image: url('https://sumitomoelectric.com/sites/default/files/styles/crop_sei_cp_1920x360/public/2023-01/products/hero_banner/DSC_0358s.JPG?h=a7430f65&itok=H_iO9zNx'); /* 替換成您的圖片URL */
    background-size: cover; /* 覆蓋整個元素 */
    background-position: center; /* 圖片居中 */
    opacity: 0.5; /* 設置淡化效果 */
    z-index: -1; /* 確保文字在偽元素之上 */
}


  label, input, button, a { display: block; margin-top: 10px; }
  input, button { padding: 10px; width: 300px; }
  button { background-color: #4CAF50; color: white; border: none; cursor: pointer; }
  #results { display: flex; justify-content: space-between; width: 100%; background-color: white; padding: 20px; }
  .result-section { width: 50%; padding: 10px; }
  .note { margin-top: 20px; }
  .credits { position: fixed; bottom: 10px; right: 10px; font-size: 12px; }
  .disclaimer { text-align: center; font-size: 10px; opacity: 0.6; margin-top: 20px; }
  .tooltip { position: relative; display: inline-block; border-bottom: 1px dotted black; }
  .tooltip-text { visibility: hidden; width: 280px; background-color: black; color: #fff; text-align: center; border-radius: 6px; padding: 5px 0; position: absolute; z-index: 1; bottom: 125%; left: 50%; margin-left: -60px; }
  .tooltip-text::after { content: ""; position: absolute; top: 100%; left: 50%; margin-left: -5px; border-width: 5px; border-style: solid; border-color: black transparent transparent transparent; }
  .tooltip:hover .tooltip-text { visibility: visible; }

</style>
</head>
<body>
<div class="header">
 <h2>風電廠商投資儲能效益評估</h2>
</div>

<form onsubmit="calculateResults(); return false;">
  <label for="capacity">風廠裝置容量 (MW):</label>
  <input type="number" id="capacity" required>
  <label for="factor">離岸風電容量因數 (%): <span class="tooltip">[?]<span class="tooltip-text">台灣離岸風電容量因數夏季約為20%，冬季可達60%</span></span></label>
  <input type="number" id="factor" required placeholder="請輸入0-100的數值">
  <a href="https://www.moeaea.gov.tw/ecw/populace/news/News.aspx?kind=9&menu_id=4360&news_id=16135" target="_blank" class="link-preview">參考數值</a>
  <a href="https://zh.wikipedia.org/zh-tw/%E5%AE%B9%E9%87

%8F%E5%9B%A0%E5%AD%90" target="_blank" class="link-preview">容量因數定義</a>
  <button type="submit">計算結果</button>
  <br> <!-- 空行 -->
</form>

<div id="results">
  <div class="result-section">
    <h2>日本北海道公司招募風電廠商投資儲能試算</h2>
    <p>風電躉售年收入: <span id="incomeJP"></span></p>
    <p>蓄電池負擔費用: <span id="investmentJP"></span></p>
    <p>回收日數: <span id="recoveryJP"></span></p>
  </div>
  <div class="result-section">
    <h2>台灣招募風電廠商投資儲能條件試算</h2>
    <p>風電躉售年收入: <span id="incomeTW"></span></p>
    <p>蓄電池負擔費用: <span id="investmentTW"></span></p>
    <p>回收日數: <span id="recoveryTW"></span></p>
  </div>
</div>

<div class="note">
  <p>備註1: 北海道電力公司對風電業者募資蓄電池的一律負擔金單價 39,000 日幣/kW<a href="https://www.hepco.co.jp/info/2018/__icsFiles/afieldfile/2019/02/20/190220b.pdf" target="_blank">詳情</a></p>
  <p>備註2: 匯率轉換 1日幣 = <span id="exchangeRate">XX</span>台幣<a href="https://rate.bot.com.tw/xrt?Lang=zh-TW" target="_blank">匯率查詢</a></p>
</div>

<div class="credits">
  製作人: Jheng Jhe, Yu, 2024.04 - Pacific Electric Wire & Cable CO. LTD <img src="https://upload.wikimedia.org/wikipedia/zh/thumb/d/d8/Pacific_Electric_Wire_%26_Cable_logo.svg/1200px-Pacific_Electric_Wire_%26_Cable_logo.svg.png" alt="公司商標" style="width: 30px;">
</div>

<div class="disclaimer">
  關於本頁資訊僅供參考，精準的效益評估需要有更多的資訊轉換及計算，此處不承擔任何法律責任，若有資料引用，請連繫製作人員。
</div>

<script>
async function calculateResults() {
    const rate = await fetchExchangeRate();
    document.getElementById('exchangeRate').textContent = rate.toFixed(2); // 更新匯率顯示
    var capacity = parseFloat(document.getElementById('capacity').value);
    var factor = parseFloat(document.getElementById('factor').value);
    var annualIncomeJP = 24 * 1000 * capacity * (factor / 100) * 24 * 365;
    var annualIncomeTW = 4.5 * 1000 * capacity * (factor / 100) * 24 * 365;
    var investmentJP = 39000 * 1000 * capacity;
    var investmentTW = 39000 * 1000 * capacity * rate; // 使用實際匯率計算
    var recoveryDaysJP = (investmentJP / annualIncomeJP) * 365;
    var recoveryDaysTW = (investmentTW / annualIncomeTW) * 365;

    document.getElementById('incomeJP').innerText = numberFormat(annualIncomeJP) + ' 日元';
    document.getElementById('incomeTW').innerText = numberFormat(annualIncomeTW) + ' 台幣';
    document.getElementById('investmentJP').innerText = numberFormat(investmentJP) + ' 日元';
    document.getElementById('investmentTW').innerText = numberFormat(investmentTW) + ' 台幣';
    document.getElementById('recoveryJP').innerText = recoveryDaysJP.toFixed(0) + ' 天';
    document.getElementById('recoveryTW').innerText = recoveryDaysTW.toFixed(0) + ' 天';
}

function numberFormat(number) {
    return number.toLocaleString('en-US');
}

async function fetchExchangeRate() {
    try {
        const response = await fetch('https://api.exchangerate-api.com/v4/latest/JPY');
        const data = await response.json();
        return data.rates.TWD; // 獲取日幣兌台幣的匯率
    } catch (error) {
        console.error('匯率獲取失敗:', error);
        return 0.22; // 返回一個預設值以免影響計算
    }
}
</script>

</body>
</html>
