<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ILLIT LIVE 시뮬레이터 - 핑크 에디션</title>
    <style>
        :root {
            --soft-pink: #FFC0CB;
            --hot-pink: #FF69B4;
            --main-pink: #FF85A2;
            --bg-pink: #FFF0F5;
            --podo: #8a2be2;
        }

        body { font-family: 'Pretendard', sans-serif; margin: 0; background: #ffe4e1; display: flex; justify-content: center; overflow: hidden; }
        .mobile-view { width: 100%; max-width: 430px; height: 100vh; background: white; position: relative; overflow: hidden; border-radius: 30px; box-shadow: 0 10px 30px rgba(0,0,0,0.1); border: 8px solid white; margin-top: 10px; }
        .screen { display: none; width: 100%; height: 100%; position: absolute; top: 0; left: 0; border-radius: 20px; overflow-y: auto; }
        .active { display: block; }

        /* 공통 헤더 */
        header { background: var(--main-pink); color: white; height: 60px; display: flex; align-items: center; justify-content: center; font-weight: bold; border-radius: 20px 20px 0 0; }

        /* 1. 예매 안내 (둥글둥글 디자인) */
        .notice-card { margin: 20px; padding: 20px; background: var(--bg-pink); border-radius: 20px; border: 2px dashed var(--hot-pink); }
        .timer-circle { width: 120px; height: 120px; border-radius: 50%; background: white; border: 5px solid var(--soft-pink); display: flex; flex-direction: column; align-items: center; justify-content: center; margin: 10px auto; }
        #timer { font-size: 20px; font-weight: bold; color: var(--hot-pink); }

        .btn-pink { width: 90%; height: 55px; background: #ccc; border: none; color: white; font-size: 18px; font-weight: bold; border-radius: 27px; margin: 10px 5%; cursor: not-allowed; transition: 0.3s; }
        .btn-pink.active-btn { background: var(--hot-pink); cursor: pointer; box-shadow: 0 4px 15px rgba(255, 105, 180, 0.4); }

        /* 2. 대기열 (대기시간) */
        .waiting-box { text-align: center; padding-top: 80px; }
        .progress-container { width: 80%; height: 20px; background: #eee; border-radius: 10px; margin: 20px auto; overflow: hidden; border: 2px solid var(--soft-pink); }
        .progress-bar { width: 0%; height: 100%; background: var(--main-pink); transition: 0.5s linear; }

        /* 3. 핑크 달력 (둥근 스타일) */
        .calendar { width: 90%; margin: 10px auto; border-collapse: separate; border-spacing: 5px; }
        .calendar td { height: 45px; text-align: center; border-radius: 12px; font-weight: bold; cursor: pointer; background: #fffafb; }
        .calendar td.active-day { background: var(--soft-pink); color: white; }
        .calendar td.selected { background: var(--hot-pink) !important; color: white; transform: scale(1.1); }

        /* 4. 핑크 보안문자 */
        .captcha-area { text-align: center; padding: 20px; }
        .captcha-box { background: var(--bg-pink); color: var(--hot-pink); font-size: 40px; font-weight: 900; letter-spacing: 8px; padding: 25px; border-radius: 15px; border: 3px solid var(--soft-pink); display: inline-block; margin-bottom: 20px; }
        .pink-input { width: 80%; height: 50px; border-radius: 25px; border: 2px solid var(--soft-pink); text-align: center; font-size: 20px; outline: none; color: var(--hot-pink); }

        /* 5. 좌석 배치도 (첫 번째 사진 배치 반영) */
        .stage { width: 180px; height: 40px; background: #eee; margin: 20px auto; text-align: center; line-height: 40px; border-radius: 5px; color: #999; font-weight: bold; }
        .seat-map { position: relative; padding: 10px; text-align: center; }
        .map-flex { display: flex; justify-content: center; gap: 5px; margin-bottom: 5px; }
        .area-block { width: 70px; height: 90px; border-radius: 10px; border: 2px solid #ddd; background: white; font-size: 11px; display: flex; flex-direction: column; align-items: center; justify-content: center; cursor: pointer; }
        .area-block.pink-zone { border-color: var(--hot-pink); background: #fff0f5; color: var(--hot-pink); font-weight: bold; }

        /* 6. 상세 좌석 (포도알) */
        .podo-grid { display: grid; grid-template-columns: repeat(12, 1fr); gap: 4px; padding: 15px; }
        .podo { width: 22px; height: 22px; border-radius: 5px; cursor: pointer; }
        .podo.purple { background: var(--podo); border: 1px solid #721cb8; }
        .podo.gray { background: #e0e0e0; cursor: default; }

        /* 7. 결제창 */
        .pay-info { padding: 20px; background: var(--bg-pink); margin: 20px; border-radius: 20px; }
        .pay-btn-group { display: flex; gap: 10px; padding: 0 20px; }
        .pay-btn { flex: 1; height: 50px; border-radius: 15px; border: 2px solid var(--soft-pink); background: white; cursor: pointer; }
        .pay-btn.selected { background: var(--hot-pink); color: white; border-color: var(--hot-pink); }
    </style>
</head>
<body>

<div class="mobile-view">
    <div id="step1" class="screen active">
        <header>ILLIT LIVE 'PRESS START♥'</header>
        <div class="notice-card">
            <h3 style="color:var(--hot-pink); text-align:center;"> 예매 안내 </h3>
            <p style="font-size:12px; color:#777; line-height:1.6;">-  회차당 1인 2매까지 예매 가능합니다. <br>- 안심예매 단계로 보안 문자를 입력해야 합니다. <br>- 정각 00초에 '예매하기' 버튼이 활성화됩니다. </p>
        </div>
        <div class="timer-circle">
            <span style="font-size:12px; color:#999;">서버시간</span>
            <span id="timer">00:00:00</span>
        </div>
        <button id="mainBtn" class="btn-pink" onclick="goWait()" style="position:absolute; bottom:20px;">예매하기</button>
    </div>

    <div id="stepWait" class="screen">
        <div class="waiting-box">
            <img src="https://img.icons8.com/bubbles/100/hearts.png"/>
            <h3 style="color:var(--hot-pink);">잠시만 기다려주세요!</h3>
            <p style="font-size:13px;">고객님 앞에 <span id="waitCount" style="font-weight:bold; color:var(--hot-pink);">78,214</span>명이 있어요.</p>
            <div class="progress-container"><div id="waitBar" class="progress-bar"></div></div>
            <p style="font-size:11px; color:#aaa;"></p>
        </div>
    </div>

    <div id="stepDate" class="screen">
        <header>날짜 선택</header>
        <div style="padding:20px;">
            <h3 style="text-align:center; color:var(--hot-pink);">2026.03</h3>
            <table class="calendar">
                <tr style="color:var(--hot-pink); font-size:12px;"><th>일</th><th>월</th><th>화</th><th>수</th><th>목</th><th>금</th><th>토</th></tr>
                <tr><td>8</td><td>9</td><td>10</td><td>11</td><td>12</td><td>13</td><td class="active-day" onclick="selectDay(this, '14일')">14</td></tr>
                <tr><td class="active-day" onclick="selectDay(this, '15일')">15</td><td>16</td><td>17</td><td>18</td><td>19</td><td>20</td><td>21</td></tr>
            </table>
            <div id="nextBtnDate" style="display:none; text-align:center; margin-top:20px;">
                <button class="btn-pink active-btn" onclick="goCaptcha()">회차 선택 완료</button>
            </div>
        </div>
    </div>

    <div id="stepCaptcha" class="screen">
        <header>안심예매</header>
        <div class="captcha-area">
            <div class="captcha-box" id="captchaText">PINKY</div>
            <p style="color:var(--hot-pink); font-size:13px;">위의 보안 문자를 입력해주세요!</p>
            <input type="text" id="captchaInput" class="pink-input" placeholder="여기에 입력!">
            <button class="btn-pink active-btn" style="margin-top:30px;" onclick="checkCaptcha()">입력 완료</button>
        </div>
    </div>

    <div id="stepMap" class="screen">
        <header>구역 선택</header>
        <div class="stage">STAGE</div>
        <div class="seat-map">
            <div class="map-flex">
                <div class="area-block pink-zone" onclick="goPodo('A')">A</div>
                <div class="area-block pink-zone" onclick="goPodo('B')">B</div>
                <div class="area-block pink-zone" onclick="goPodo('C')">C</div>
                <div class="area-block pink-zone" onclick="goPodo('D')">D</div>
            </div>
            <div class="map-flex" style="flex-wrap: wrap; width: 300px; margin: 0 auto;">
                <div class="area-block" onclick="goPodo('101')" style="width:45px; height:45px; margin:2px;">101</div>
                <div class="area-block" onclick="goPodo('102')" style="width:45px; height:45px; margin:2px;">102</div>
                <div class="area-block" onclick="goPodo('103')" style="width:45px; height:45px; margin:2px;">103</div>
                <div class="area-block" onclick="goPodo('104')" style="width:45px; height:45px; margin:2px;">104</div>
            </div>
        </div>
    </div>

    <div id="stepPodo" class="screen">
        <header>좌석 선택 <span style="float:right; padding-right:15px;" onclick="go('stepMap')">✕</span></header>
        <div style="background:#eee; padding:5px 15px; font-size:11px; display:flex; justify-content:space-between;">
            <span>구역: <b id="podoTitle">A</b></span>
            <span>잔여: <b style="color:var(--podo);">8석</b></span>
        </div>
        <div class="podo-grid" id="podoGrid"></div>
        <p style="text-align:center; font-size:11px; color:#999;">보라색 포도알을 빠르게 터치!</p>
    </div>

    <div id="stepPay" class="screen">
        <header>결제하기</header>
        <div class="pay-info">
            <p>선택좌석: <b id="finalSeat">A구역 15번</b></p>
            <p>티켓금액: 165,000원</p>
            <p>수수료: 2,000원</p>
            <hr border="1" style="border-color:white;">
            <p style="color:var(--hot-pink); font-size:18px;">총 결제금액: 167,000원</p>
        </div>
        <div class="pay-btn-group">
            <button class="pay-btn" id="cardBtn" onclick="selectPay('card')">신용카드</button>
            <button class="pay-btn" id="bankBtn" onclick="selectPay('bank')">무통장입금</button>
        </div>
        <button class="btn-pink active-btn" style="margin-top:20px;" onclick="finish()">최종 결제 완료</button>
    </div>
</div>

<script>
    /* 1. 서버 타이머 */
    setInterval(() => {
        const now = new Date();
        const s = now.getSeconds();
        const ms = Math.floor(now.getMilliseconds()/100);
        document.getElementById('timer').innerText = now.toTimeString().split(' ')[0] + ":" + ms;
        const btn = document.getElementById('mainBtn');
        if(s === 0) { btn.classList.add('active-btn'); btn.disabled = false; }
        else { btn.classList.remove('active-btn'); btn.disabled = true; }
    }, 100);

    function go(id) {
        document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
        document.getElementById(id).classList.add('active');
    }

    /* 2. 대기열 */
    function goWait() {
        go('stepWait');
        let count = 78214;
        let prog = 0;
        const iv = setInterval(() => {
            count -= 452;
            prog += 0.8; // 더 천천히 차도록 수정
            document.getElementById('waitCount').innerText = Math.max(0, count).toLocaleString();
            document.getElementById('waitBar').style.width = prog + "%";
            if(prog >= 100) { clearInterval(iv); go('stepDate'); }
        }, 150);
    }

    /* 3. 날짜 및 보안문자 */
    function selectDay(el, txt) {
        document.querySelectorAll('.calendar td').forEach(td => td.classList.remove('selected'));
        el.classList.add('selected');
        document.getElementById('nextBtnDate').style.display = 'block';
    }

    function goCaptcha() {
        const chars = "ABCDEFGHJKLMNPQRSTUVWXYZ23456789";
        let code = "";
        for(let i=0; i<6; i++) code += chars.charAt(Math.floor(Math.random() * chars.length));
        document.getElementById('captchaText').innerText = code;
        go('stepCaptcha');
    }

    function checkCaptcha() {
        if(document.getElementById('captchaInput').value.toUpperCase() === document.getElementById('captchaText').innerText) go('stepMap');
        else { alert("앗! 문자가 틀렸어요!"); goCaptcha(); }
    }

    /* 4. 포도알 (극악 난이도 + 이선좌) */
    function goPodo(name) {
        document.getElementById('podoTitle').innerText = name;
        const grid = document.getElementById('podoGrid');
        grid.innerHTML = '';
        go('stepPodo');
        for(let i=0; i<150; i++) {
            const p = document.createElement('div');
            const isPodo = Math.random() < 0.03; // 포도알 확률 3% (매우 어려움)
            p.className = 'podo ' + (isPodo ? 'purple' : 'gray');
            p.onclick = function() {
                if(this.classList.contains('purple')) {
                    // 이선좌 확률 85% 설정
                    if(Math.random() < 0.85) {
                        alert("이미 선택된 좌석입니다.");
                        this.className = 'podo gray';
                    } else {
                        this.style.background = '#ff0000';
                        setTimeout(() => {
                            document.getElementById('finalSeat').innerText = name + "구역 " + (i+1) + "번";
                            go('stepPay');
                        }, 200);
                    }
                }
            };
            grid.appendChild(p);
        }
    }

    /* 5. 결제 및 마무리 */
    let selectedMethod = 'card';
    function selectPay(method) {
        selectedMethod = method;
        document.getElementById('cardBtn').className = 'pay-btn' + (method === 'card' ? ' selected' : '');
        document.getElementById('bankBtn').className = 'pay-btn' + (method === 'bank' ? ' selected' : '');
    }

    function finish() {
        if(selectedMethod === 'card') {
            alert("🎉 결제 성공! ILLIT 공연 예매가 완료되었습니다. 카드 승인이 완료되었습니다!");
        } else {
            alert("🎉 예매 성공! 내일까지 입금하지 않으면 자동 취소되니 주의하세요!");
        }
        location.reload();
    }
</script>

</body>
