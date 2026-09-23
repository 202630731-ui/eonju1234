# eonju1234<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>오늘 뭐 먹지? | 결정 장애 해결사</title>
    <style>
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Pretendard', -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
        }

        body {
            background-color: #f7f9fc;
            color: #333;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            padding: 20px;
        }

        .container {
            background: #ffffff;
            width: 100%;
            max-width: 480px;
            border-radius: 20px;
            box-shadow: 0 10px 25px rgba(0,0,0,0.08);
            padding: 30px;
            text-align: center;
        }

        h1 {
            font-size: 1.6rem;
            color: #2c3e50;
            margin-bottom: 8px;
        }

        p.subtitle {
            font-size: 0.95rem;
            color: #7f8c8d;
            margin-bottom: 25px;
        }

        .step {
            display: none;
        }

        .step.active {
            display: block;
            animation: fadeIn 0.4s ease-in-out;
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }

        .step-title {
            font-size: 1.1rem;
            font-weight: 600;
            margin-bottom: 15px;
            color: #34495e;
        }

        .btn-group {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 12px;
        }

        .btn {
            background-color: #f1f3f5;
            border: 2px solid transparent;
            padding: 14px;
            border-radius: 12px;
            font-size: 1rem;
            font-weight: 600;
            color: #495057;
            cursor: pointer;
            transition: all 0.2s ease;
        }

        .btn:hover {
            background-color: #ff6b6b;
            color: white;
            transform: translateY(-2px);
            box-shadow: 0 4px 12px rgba(255, 107, 107, 0.3);
        }

        /* Result View */
        .result-card {
            background: #fff5f5;
            border-radius: 16px;
            padding: 20px;
            margin-top: 10px;
            border: 1px solid #ffe3e3;
        }

        .food-name {
            font-size: 1.8rem;
            font-weight: 700;
            color: #e03131;
            margin-bottom: 15px;
        }

        .img-wrapper {
            width: 100%;
            height: 250px;
            border-radius: 12px;
            overflow: hidden;
            margin-bottom: 15px;
            background-color: #e9ecef;
        }

        .img-wrapper img {
            width: 100%;
            height: 100%;
            object-fit: cover;
        }

        .reset-btn {
            width: 100%;
            margin-top: 20px;
            background-color: #339af0;
            color: white;
        }

        .reset-btn:hover {
            background-color: #1c7ed6;
            color: white;
            box-shadow: 0 4px 12px rgba(28, 126, 214, 0.3);
        }
    </style>
</head>
<body>

<div class="container">
    <h1>오늘 뭐 먹지? 🍽️</h1>
    <p class="subtitle">후회 없는 완벽한 식사 선택 가이드</p>

    <!-- STEP 1: 카테고리 선택 -->
    <div id="step1" class="step active">
        <div class="step-title">1. 어떤 스타일의 음식이 끌리시나요?</div>
        <div class="btn-group">
            <button class="btn" onclick="selectCategory('중식')">중식</button>
            <button class="btn" onclick="selectCategory('일식')">일식</button>
            <button class="btn" onclick="selectCategory('양식')">양식</button>
            <button class="btn" onclick="selectCategory('기타')">기타 (한식)</button>
        </div>
    </div>

    <!-- STEP 2: 종류 선택 -->
    <div id="step2" class="step">
        <div class="step-title">2. 선호하는 음식 종류를 골라주세요.</div>
        <div class="btn-group">
            <button class="btn" onclick="selectType('밥류')">🍚 밥류</button>
            <button class="btn" onclick="selectType('국류')">🍲 국/탕류</button>
            <button class="btn" onclick="selectType('면류')">🍜 면류</button>
            <button class="btn" onclick="selectType('육류')">🥩 육류</button>
            <button class="btn" style="grid-column: span 2;" onclick="selectType('생선류')">🐟 생선류</button>
        </div>
    </div>

    <!-- STEP 3: 최종 결과 -->
    <div id="step3" class="step">
        <div class="step-title">오늘의 추천 메뉴는?</div>
        <div class="result-card">
            <div id="foodName" class="food-name"></div>
            <div class="img-wrapper">
                <img id="foodImg" src="" alt="음식 이미지">
            </div>
        </div>
        <button class="btn reset-btn" onclick="reset()">다시 고르기 🔄</button>
    </div>
</div>

<script>
    // 카테고리별 중복 없는 메뉴 데이터 및 고해상도 이미지 연결
    const foodDatabase = {
        '중식': {
            '밥류': { name: '마파두부밥', img: 'https://encrypted-tbn3.gstatic.com/licensed-image?q=tbn:ANd9GcRogrla8V1A_6r7pWJ3wt7p_-yKhxHUcTCUWp5UsDhXOPkXyvh9wa-Msz8HdZ1f0_dLoicRrVCLVX_peWg' },
            '국류': { name: '짬뽕', img: 'https://encrypted-tbn0.gstatic.com/licensed-image?q=tbn:ANd9GcQqmOnomGTGLtuf2w7a9aTfhVBuXkg_m_kNA0AgFSYd4xXmBEYzReyNjwJ3qN016zuLH_xQeht1lkE8NZs' },
            '면류': { name: '짜장면', img: 'https://encrypted-tbn0.gstatic.com/licensed-image?q=tbn:ANd9GcQkODUyDGVgcESTAV_ly6aErBTUjMrhb-IZzf5BV33RYM2dVwmjAc1Bu5uh_NPi96sTwZY0Op_btj0XZ48' },
            '육류': { name: '탕수육', img: 'https://encrypted-tbn2.gstatic.com/licensed-image?q=tbn:ANd9GcRgBqDiZ5ZwxWZ5ql8QsuhjzeGrqUiivWrCae7_i9Q4_Th8k6xwk6esiH4tgsTMIue35kLImnA2hgeC7Mc' },
            '생선류': { name: '동파육/생선탕수', img: 'https://images.unsplash.com/photo-1563245372-f21724e3856d?w=600' }
        },
        '일식': {
            '밥류': { name: '초밥 (스시)', img: 'https://encrypted-tbn3.gstatic.com/licensed-image?q=tbn:ANd9GcSatjTCBCEvdIpyB0-ut30OWCKQrK6RrXpipHotkLs8IvY7K9UlvdCvpbIU52Uu1GbhpGjj1rUj02cKg-U' },
            '국류': { name: '돈코츠 라멘', img: 'https://encrypted-tbn1.gstatic.com/licensed-image?q=tbn:ANd9GcQjlP0RRhZsknM9BpDBDK5_y7bFEQopsS29DUBOkTN6p390Hv0CclObOnIbT3bSEqbZsyIhkeArGaZcCw8' },
            '면류': { name: '우동', img: 'https://images.unsplash.com/photo-1618841557871-b4664fbf0cb3?w=600' },
            '육류': { name: '돈가스', img: 'https://encrypted-tbn0.gstatic.com/licensed-image?q=tbn:ANd9GcQKRYmg0g-FyhKq6PdK57mr5-X_vsZ80mFg3STPhR5-B9QKHIJcr0u9cpRckHzuEBnla5iU9o6gTZD8e9U' },
            '생선류': { name: '연어덮밥 (사케동)', img: 'https://images.unsplash.com/photo-1546069901-ba9599a7e63c?w=600' }
        },
        '양식': {
            '밥류': { name: '리조또', img: 'https://semie.cooking/image/contents/recipe/db/wd/dqouocny/160762363yjvs.jpg' },
            '국류': { name: '크림 스프', img: 'https://encrypted-tbn2.gstatic.com/licensed-image?q=tbn:ANd9GcRcJJekhHFtPlwUEl5-MyRaw8fixzI43KRbmMJLLB3zVSOJyNyW10u_yTVuDFKBgBNygFSN0rj48VwYiOE' },
            '면류': { name: '파스타', img: 'https://encrypted-tbn1.gstatic.com/licensed-image?q=tbn:ANd9GcSVOTVJ00PSxyt-cRkybMWJeljwTWzbpiYBEQeqXM56by9GgdwIL_gqE6HupwCo2ox51zl6Q9xBNO6j1Pc' },
            '육류': { name: '비프 스테이크', img: 'https://encrypted-tbn2.gstatic.com/licensed-image?q=tbn:ANd9GcRHCu8CCV5yy_nBOxuvFaKApI8qpEu59cZ6ThGLC6rbPXZfeC9Za7tBujVlFzuVfK1llRlqS8r49lKW-Pc' },
            '생선류': { name: '연어 스테이크', img: 'https://encrypted-tbn1.gstatic.com/licensed-image?q=tbn:ANd9GcRCJXpyiq9jdIjxQssCFuqY-rs9DtbML8vibwziCddLxtoAefnk6UcV8cVEH4dRyhZAxA6iMrPLbA9MKfw' }
        },
        '기타': {
            '밥류': { name: '비빔밥', img: 'https://encrypted-tbn0.gstatic.com/licensed-image?q=tbn:ANd9GcTj50o_MjBzNcjWzL4ZvQUOFzJSpfBpDjFnzxAMgX-6d5mw09DCsiZkMyoBdJ253sF68TMOFiQz_kpkgr8' },
            '국류': { name: '김치찌개', img: 'https://encrypted-tbn1.gstatic.com/licensed-image?q=tbn:ANd9GcQrHBa874iCtTJKG5xNlVW0ckhR0qN4Nfy-1OfPRO0xOVvY5DrwVsO0he6hTRiTBwKeAek4Xiw8-EowXWI' },
            '면류': { name: '칼국수', img: 'https://encrypted-tbn0.gstatic.com/licensed-image?q=tbn:ANd9GcTxlcYdtYv-3pC5SwEeNdzEZzbZQJ8Z80bFSxZv17Mo3Vm23R0SKA4WiXW4zmKL_hjBLVvSufsml2M2djI' },
            '육류': { name: '돼지 불고기', img: 'https://encrypted-tbn3.gstatic.com/licensed-image?q=tbn:ANd9GcTaiYUyv1OZaPde5fa6QCfvtCd2OjVzWNtf_oFKy-tP8Q7S2Rv0QUINBKOPX8xmsmeXukPWLHIpuVMFWpQ' },
            '생선류': { name: '고등어 구이', img: 'https://encrypted-tbn1.gstatic.com/licensed-image?q=tbn:ANd9GcRCq1vnd7A27SZ4isImMjmjPCcNd6vt6Sa3xc46i_uiZRUk5rzhC7i-WtAr99uQWDluWE9BzjsnIOYPaVs' }
        }
    };

    let selectedCategory = '';
    let selectedType = '';

    function selectCategory(category) {
        selectedCategory = category;
        document.getElementById('step1').classList.remove('active');
        document.getElementById('step2').classList.add('active');
    }

    function selectType(type) {
        selectedType = type;
        const result = foodDatabase[selectedCategory][selectedType];

        document.getElementById('foodName').innerText = result.name;
        document.getElementById('foodImg').src = result.img;

        document.getElementById('step2').classList.remove('active');
        document.getElementById('step3').classList.add('active');
    }

    function reset() {
        selectedCategory = '';
        selectedType = '';
        document.getElementById('step3').classList.remove('active');
        document.getElementById('step1').classList.add('active');
    }
</script>

</body>
</html>
