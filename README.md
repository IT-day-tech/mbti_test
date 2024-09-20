<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MBTI 테스트</title>
    <style>
        body {
            font-family: 'Arial', sans-serif;
            margin: 0;
            padding: 0;
            background-image: linear-gradient(to right, #ff6f00, #ffb74d);
            color: #333;
        }
        .container {
            max-width: 700px;
            margin: 50px auto;
            padding: 30px;
            background: rgba(255, 255, 255, 0.9);
            border-radius: 10px;
            box-shadow: 0 0 20px rgba(0, 0, 0, 0.3);
            text-align: center;
        }
        h1, h2 {
            margin-bottom: 20px;
            color: #d32f2f;
        }
        .question {
            margin: 30px 0;
            font-size: 18px;
        }
        button {
            display: inline-block;
            width: 45%;
            padding: 15px;
            margin: 10px 2%;
            background-color: #1976d2;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            transition: background 0.3s;
            font-size: 16px;
        }
        button:hover {
            background-color: #004BA0;
        }
        #result {
            margin-top: 20px;
            display: none;
        }
        .pokemon {
            margin: 15px 0;
            width: 200px;
            height: auto;
        }
        .pokemon-name {
            font-weight: bold;
            font-size: 24px;
            margin-top: 10px;
        }
        .percentage {
            font-weight: bold;
            font-size: 18px;
        }
    </style>
</head>
<body>

<div class="container">
    <h1>MBTI 테스트</h1>
    <div id="quiz">
        <div class="question">
            <p>1. 새로운 사람과 쉽게 이야기를 나누는 편이다.</p>
            <button onclick="nextQuestion(1)">네</button>
            <button onclick="nextQuestion(2)">아니요</button>
        </div>
    </div>
    <div id="result">
        <h2>결과: <span id="personalityType"></span></h2>
        <img class="pokemon" id="pokemonImage" src="" alt="포켓몬 이미지" />
        <div class="pokemon-name">포켓몬 이름: <span id="pokemonName"></span></div>
        <div class="percentage">비율: <span id="animalPercentage"></span>%</div>
        <p id="description"></p>
        <p id="detailedPercentages"></p>
        <p id="analysis"></p>
    </div>
</div>

<script>
    let score = { E: 0, I: 0, S: 0, N: 0, T: 0, F: 0, J: 0, P: 0 };
    let currentQuestion = 0;

    const questions = [
        { q: "1. 새로운 사람과 쉽게 이야기를 나누는 편이다.", type: "E", answer: [1, 0] },
        { q: "2. 혼자 있는 것이 편하다.", type: "I", answer: [0, 1] },
        { q: "3. 기계나 도구를 잘 이해하지 못한다.", type: "S", answer: [0, 1] },
        { q: "4. 논리적인 결정을 선호한다.", type: "T", answer: [1, 0] },
        { q: "5. 친구들과 자주 만나는 편이다.", type: "E", answer: [1, 0] },
        { q: "6. 직관적으로 상황을 판단하는 편이다.", type: "N", answer: [1, 0] },
        { q: "7. 규칙을 따르는 것을 중요하게 생각한다.", type: "J", answer: [1, 0] },
        { q: "8. 새로운 경험을 즐긴다.", type: "P", answer: [1, 0] },
        { q: "9. 감정적으로 문제를 이해하는 것이 어렵다.", type: "T", answer: [1, 0] },
        { q: "10. 사람들과 쉽게 친해진다.", type: "E", answer: [1, 0] },
        { q: "11. 어떤 상황에서도 나의 감정을 잘 표현할 수 있다.", type: "F", answer: [1, 0] },
        { q: "12. 혼자 있는 것은 때로는 힘들고 외롭다.", type: "E", answer: [1, 0] },
        { q: "13. 세부 사항에 신경 쓰는 편이다.", type: "S", answer: [1, 0] },
        { q: "14. 직관력을 중시하는 편이다.", type: "N", answer: [1, 0] },
        { q: "15. 단순하게 대화를 선호하는 편이다.", type: "J", answer: [1, 0] },
        { q: "16. 현실적인 문제 해결을 선호한다.", type: "T", answer: [1, 0] },
        { q: "17. 친구들과 공동의 활동을 하는 것을 좋아한다.", type: "E", answer: [1, 0] },
        { q: "18. 나의 감정에 따라 결정하는 편이다.", type: "F", answer: [1, 0] },
        { q: "19. 세부 정보를 무시하는 편이다.", type: "P", answer: [1, 0] },
        { q: "20. 문제를 해결할 때 다양한 가능성을 고려한다.", type: "N", answer: [1, 0] }
    ];

    const situationQuestions = [
        { q: "당신이 회사의 새로운 프로젝트 팀에 선발되었습니다. 어떻게 행동하시겠습니까?",
          options: [ { text: "팀원들과 즉시 아이디어를 논의한다.", type: "E" },
                     { text: "혼자서 아이디어를 구상하고 준비한다.", type: "I" } ] },
        { q: "친구들과 여름 여행을 계획하고 있습니다. 당신의 역할은?",
          options: [ { text: "모든 세부 계획을 세운다.", type: "J" },
                     { text: "자유롭게 결정하도록 맡긴다.", type: "P" } ] },
        { q: "상황이 복잡해질 때 어떻게 반응하십니까?",
          options: [ { text: "논리적으로 분석하며 해결책을 찾는다.", type: "T" },
                     { text: "감정적으로 상황을 느끼고 대처한다.", type: "F" } ] },
        { q: "상상력 있는 이야기를 듣는 것을 더 선호합니까, 아니면 사실적인 이야기를 선호합니까?",
          options: [ { text: "상상력 있는 이야기.", type: "N" },
                     { text: "사실적인 이야기.", type: "S" } ] },
        { q: "주의력이 산만해질 때 어떻게 집중합니까?",
          options: [ { text: "나는 극복 방법을 찾는다.", type: "E" },
                     { text: "조용한 환경에서 혼자 해결하려고 한다.", type: "I" } ] },
        { q: "최신 트렌드나 유행에 대해 신경 쓰십니까?",
          options: [ { text: "네, 항상 관심을 가지며 따라간다.", type: "E" },
                     { text: "아니요, 내 관심을 유지한다.", type: "I" } ] },
        { q: "두 사람의 문제를 중재할 때, 당신은 어떻게 행동하시나요?",
          options: [ { text: "양쪽 의견을 들으며 해결책을 색출한다.", type: "T" },
                     { text: "각자의 감정을 이해하고 공감한다.", type: "F" } ] },
        { q: "새로운 경험에 대한 당신의 태도는?",
          options: [ { text: "항상 새로운 것을 시도하는 것을 좋아한다.", type: "P" },
                     { text: "계획대로 진행하는 것이 좋다.", type: "J" } ] },
        { q: "집중할 때 어떤 방식이 더 도움이 됩니까?",
          options: [ { text: "상황을 전체적으로 바라본다.", type: "N" },
                     { text: "세부적으로 하나씩 집중한다.", type: "S" } ] },
        { q: "팀 프로젝트에서의 당신은 어떤 역할을 주로 맡게 되나요?",
          options: [ { text: "팀 분위기를 이끌고 조화를 만든다.", type: "E" },
                     { text: "주어진 역할을 충실히 수행한다.", type: "I" } ] }
    ];

    const pokemonMapping = {
        "ISTJ": { name: "안농", image: "https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/176.png" },
        "ISFJ": { name: "메리프", image: "https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/179.png" },
        "INFJ": { name: "깨비참", image: "https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/137.png" },
        "INTJ": { name: "네이티오", image: "https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/177.png" },
        "ESTP": { name: "리자몽", image: "https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/6.png" },
        "ESFP": { name: "부스터", image: "https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/136.png" },
        "ENFP": { name: "파르셀", image: "https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/54.png" },
        "ENTJ": { name: "닥트리오", image: "https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/51.png" },
        "ISTP": { name: "또가스", image: "https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/110.png" },
        "ISFP": { name: "이상해꽃", image: "https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/3.png" },
        "INFP": { name: "코일", image: "https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/93.png" },
        "INTP": { name: "그라돈", image: "https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/383.png" },
        "ESTJ": { name: "가디", image: "https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/58.png" },
        "ESFJ": { name: "비뚤이", image: "https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/102.png" },
        "ENFJ": { name: "래티아스", image: "https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/380.png" },
        "ENTP": { name: "내로", image: "https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/261.png" }
    };

    function nextQuestion(answer) {
        const current = questions[currentQuestion];
        if (answer === 1) score[current.type] += current.answer[0];
        if (answer === 2) score[current.type] += current.answer[1];
        currentQuestion++;

        // 모든 질문을 완료한 후 현재 질문 수가 질문 목록 길이보다 클 때 상황 질문으로 넘어감
        if (currentQuestion >= questions.length) {
            showSituations();
        } else {
            showNext();
        }
    }

    function showNext() {
        const current = questions[currentQuestion];
        document.querySelector('.question').innerHTML = `
            <p>${current.q}</p>
            <button onclick="nextQuestion(1)">네</button>
            <button onclick="nextQuestion(2)">아니요</button>`;
    }

    function showSituations() {
        const situationIndex = currentQuestion - questions.length; 
        if (situationIndex < situationQuestions.length) {
            const currentSituation = situationQuestions[situationIndex];
            document.querySelector('.question').innerHTML = `
                <p>${currentSituation.q}</p>
                ${currentSituation.options.map((option, index) => `
                    <button onclick="nextSituation(${index})">${option.text}</button>
                `).join('')}`;
        } else {
            showResult();
        }
    }

    function nextSituation(index) {
        const currentSituation = situationQuestions[currentQuestion - questions.length];
        score[currentSituation.options[index].type] += 1; 
        currentQuestion++;

        if (currentQuestion >= questions.length + situationQuestions.length) {
            showResult();
        } else {
            showSituations();
        }
    }

    function showResult() {
        let eScore = score.E;
        let iScore = score.I;
        let totalEI = eScore + iScore || 1;
        let eRatio = ((eScore / totalEI) * 100).toFixed(0);
        let iRatio = ((iScore / totalEI) * 100).toFixed(0);

        let sScore = score.S;
        let nScore = score.N;
        let totalSN = sScore + nScore || 1;
        let sRatio = ((sScore / totalSN) * 100).toFixed(0);
        let nRatio = ((nScore / totalSN) * 100).toFixed(0);

        let tScore = score.T;
        let fScore = score.F;
        let totalTF = tScore + fScore || 1;
        let tRatio = ((tScore / totalTF) * 100).toFixed(0);
        let fRatio = ((fScore / totalTF) * 100).toFixed(0);

        let jScore = score.J;
        let pScore = score.P;
        let totalJP = jScore + pScore || 1;
        let jRatio = ((jScore / totalJP) * 100).toFixed(0);
        let pRatio = ((pScore / totalJP) * 100).toFixed(0);

        let personality = (eScore >= iScore ? "E" : "I") + 
                         (sScore >= nScore ? "S" : "N") + 
                         (tScore >= fScore ? "T" : "F") + 
                         (jScore >= pScore ? "J" : "P");
        
        document.getElementById('personalityType').innerText = personality;

        // 포켓몬 랜덤 선택
        const selectedPokemon = pokemonMapping[personality] || { name: "알 수 없음", image: "" };
        
        document.getElementById('pokemonImage').src = selectedPokemon.image;
        document.getElementById('pokemonName').innerText = selectedPokemon.name;

        const descriptions = {
            "ISTJ": "신뢰할 수 있는 실용가.",
            "ISFJ": "보호자 유형.",
            "INFJ": "통찰력 있는 상담자.",
            "INTJ": "독창적인 전략가.",
            "ISTP": "냉철한 분석가.",
            "ISFP": "모험적인 예술가.",
            "INFP": "이상주의적인 중재자.",
            "INTP": "논리적인 사상가.",
            "ESTP": "모험가 스타일.",
            "ESFP": "사교적인 연예가.",
            "ENFP": "열정적인 창조자.",
            "ENTP": "자유로운 혁신가.",
            "ESTJ": "관리자 유형.",
            "ESFJ": "친절하고 헌신적인 유형.",
            "ENFJ": "카리스마 넘치는 리더.",
            "ENTJ": "전략적인 지휘관."
        };
        
        document.getElementById('description').innerText = descriptions[personality] || "유형을 알 수 없습니다.";

        document.getElementById('animalPercentage').innerText = Math.round((parseFloat(eRatio) + parseFloat(iRatio)) / 2);
        document.getElementById('detailedPercentages').innerText = 
            `E: ${eRatio}%, I: ${iRatio}%, S: ${sRatio}%, N: ${nRatio}%, T: ${tRatio}%, F: ${fRatio}%, J: ${jRatio}%, P: ${pRatio}%`;

        document.getElementById('analysis').innerText = 
            `당신의 ${personality}는 다음과 같은 비율로 나타났습니다. ` + 
            `E와 I의 비율은 ${eRatio}% : ${iRatio}%, ` +
            `S과 N의 비율은 ${sRatio}% : ${nRatio}%, ` +
            `T와 F의 비율은 ${tRatio}% : ${fRatio}%, ` +
            `J와 P의 비율은 ${jRatio}% : ${pRatio}%입니다. ` +
            `이는 당신의 성격 특성과 선호도를 기반으로 합니다.`;

        document.getElementById('quiz').style.display = 'none';
        document.getElementById('result').style.display = 'block';
    }
</script>

</body>
</html>
