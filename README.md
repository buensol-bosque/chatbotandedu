# chatbotandedu
chatbot을 이용한 교과융합 수업
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>독립운동가와 인터뷰하기</title>
  <link href="https://cdn.jsdelivr.net/gh/orioncactus/pretendard@v1.3.9/dist/web/static/pretendard.min.css" rel="stylesheet">
  <script src="https://cdn.tailwindcss.com"></script>
  <script>
    tailwind.config = {
      theme: {
        extend: {
          fontFamily: {
            sans: ['Pretendard', 'system-ui', 'sans-serif'],
          },
        },
      },
    };
  </script>
  <style>
    body { font-family: 'Pretendard', system-ui, sans-serif; letter-spacing: -0.01em; }
    .chat-scroll { scroll-behavior: smooth; }
    .chat-scroll::-webkit-scrollbar { width: 6px; }
    .chat-scroll::-webkit-scrollbar-thumb { background: #d1d5db; border-radius: 3px; }
    .bubble-in { animation: bubbleIn 220ms ease-out; }
    @keyframes bubbleIn {
      from { opacity: 0; transform: translateY(6px); }
      to { opacity: 1; transform: translateY(0); }
    }
    .dot { animation: dot 1.2s infinite; }
    .dot:nth-child(2) { animation-delay: 0.2s; }
    .dot:nth-child(3) { animation-delay: 0.4s; }
    @keyframes dot {
      0%, 80%, 100% { opacity: 0.2; transform: translateY(0); }
      40% { opacity: 1; transform: translateY(-3px); }
    }
  </style>
</head>
<body class="bg-gray-50 min-h-screen">
  <div id="app"></div>

  <script>
    // ====== 인물 데이터 ======
    const PEOPLE = {
      ahn: {
        id: 'ahn',
        name: '안중근',
        years: '1879 ~ 1910',
        title: '하얼빈역의 의병장',
        intro: '안녕하세요, 학생 여러분. 저는 안중근입니다. 궁금한 것이 있으면 무엇이든 물어보세요.',
        color: 'bg-amber-100 text-amber-900',
        initial: '安',
        suggested: [
          '어디서 태어나셨나요?',
          '왜 이토 히로부미를 처단하셨나요?',
          '동양평화론이 뭔가요?',
          '단지동맹은 어떻게 만드셨나요?',
          '어머니께서는 어떤 분이셨나요?',
        ],
        qa: [
          {
            keywords: ['태어', '출생', '고향', '어디서', '해주', '황해'],
            answer: '저는 1879년, 황해도 해주에서 태어났습니다. 어릴 적부터 활쏘기와 사냥을 좋아했고, 한학 공부도 함께 했지요. 가문의 어른들께서는 제가 좀 거친 면이 있다며 걱정도 하셨답니다.',
          },
          {
            keywords: ['이토', '히로부미', '처단', '저격', '거사', '하얼빈', '의거', '쏘'],
            answer: '1909년 10월 26일, 하얼빈 역에서 조선 침략의 우두머리 이토 히로부미를 처단했습니다. 이는 개인의 복수가 아니라, 우리 민족과 동양의 평화를 짓밟은 사람에게 책임을 묻는 일이었어요. 거사 후에 저는 \'대한 만세\'를 외쳤습니다.',
          },
          {
            keywords: ['평화', '동양평화', '동양', '평화론'],
            answer: '저는 감옥에서 "동양평화론"이라는 글을 쓰기 시작했습니다. 한국, 중국, 일본 세 나라가 서로 동등하게 협력해서 평화를 이루어야 한다는 생각이지요. 일본이 한국을 침략한 것은 그 평화의 약속을 먼저 깨뜨린 것입니다.',
          },
          {
            keywords: ['단지', '손가락', '손바닥', '왼손', '맹세', '동맹'],
            answer: '1909년 봄, 동지 11명과 함께 왼손 네 번째 손가락 한 마디를 잘라 그 피로 태극기 위에 \'대한독립\'이라 썼습니다. 이를 "단지동맹"이라 부릅니다. 손가락 하나를 바쳐 나라를 되찾겠다는 다짐이었지요.',
          },
          {
            keywords: ['어머니', '조마리아', '엄마', '모친', '편지'],
            answer: '제 어머니 조마리아 여사는 강하신 분이셨습니다. 제가 사형을 앞두고 있을 때 "비겁하게 목숨을 구걸하지 말고 떳떳하게 죽으라"는 편지와 함께 흰 명주옷을 지어 보내주셨습니다. 어머니의 그 마음이 제가 흔들리지 않게 한 힘이었어요.',
          },
          {
            keywords: ['천주교', '종교', '세례', '토마스', '하느님'],
            answer: '저는 천주교 신자입니다. 세례명은 토마스이지요. 열일곱 살에 세례를 받았고, 신앙은 제 삶의 큰 기둥이었습니다. 감옥에서도 매일 기도를 드렸답니다.',
          },
          {
            keywords: ['감옥', '뤼순', '여순', '갇히', '사형', '죽음', '순국', '마지막', '유언'],
            answer: '뤼순 감옥에 갇혀 다섯 달을 보냈습니다. 1910년 3월 26일, 어머니께서 지어주신 흰 명주옷을 입고 사형대에 올랐지요. 그때 제 나이 서른하나였습니다. 마지막까지 동양 평화를 위해 일하지 못한 것이 아쉬웠습니다.',
          },
          {
            keywords: ['교육', '학교', '진남포', '돈의', '삼흥'],
            answer: '독립을 이루려면 무엇보다 교육이 중요하다고 생각했습니다. 그래서 평안도 진남포에 삼흥학교와 돈의학교를 세워 학생들을 가르쳤지요. 배움이 곧 힘이라는 믿음이 있었어요.',
          },
          {
            keywords: ['글씨', '서예', '붓글씨', '유묵', '책'],
            answer: '감옥에서 많은 글씨를 남겼습니다. "하루라도 책을 읽지 않으면 입안에 가시가 돋친다(一日不讀書 口中生荊棘)"는 글귀가 가장 유명하지요. 사형 직전까지도 글을 쓰고 책을 읽었습니다.',
          },
          {
            keywords: ['어린이', '학생', '후손', '바라', '미래', '꿈', '하고 싶은 말'],
            answer: '여러분이 우리 역사를 잊지 않고, 서로를 아끼며, 이웃 나라와 평화롭게 살아가기를 바랍니다. 제가 꿈꾼 동양 평화는 아직 끝나지 않은 숙제예요. 여러분 손에 달려 있습니다.',
          },
        ],
        fallback: '음, 그 부분은 조금 더 구체적으로 물어봐 주시겠어요? 제 고향, 거사, 어머니, 동양평화론처럼 짚어주시면 자세히 답해드릴게요.',
      },

      yu: {
        id: 'yu',
        name: '유관순',
        years: '1902 ~ 1920',
        title: '아우내 장터의 소녀',
        intro: '반가워요. 저는 유관순이에요. 여러분과 같은 학생이었던 제 이야기, 들려드릴게요.',
        color: 'bg-rose-100 text-rose-900',
        initial: '柳',
        suggested: [
          '어디서 태어나셨나요?',
          '3.1 운동에 어떻게 참여하셨어요?',
          '아우내 장터 만세 시위 이야기 들려주세요',
          '서대문 형무소에서는 어떠셨어요?',
          '같은 또래 친구들에게 하고 싶은 말이 있나요?',
        ],
        qa: [
          {
            keywords: ['태어', '출생', '고향', '어디서', '천안', '병천', '충청'],
            answer: '저는 1902년 충청남도 천안 병천면 용두리에서 태어났어요. 다섯 남매 중 둘째였고, 부모님은 신앙심이 깊고 새로운 교육에도 마음이 열린 분들이셨어요.',
          },
          {
            keywords: ['학교', '이화', '이화학당', '서울', '공부', '유학'],
            answer: '서울에 있는 이화학당에서 공부했어요. 충청도 시골 아이가 서울로 올라가 새로운 세상을 배운다는 게 얼마나 두근거리던지요. 친구들과 함께 책을 읽고 토론하던 시간이 정말 좋았어요.',
          },
          {
            keywords: ['3.1', '삼일', '만세', '운동', '1919', '독립선언'],
            answer: '1919년 3월 1일, 서울에서 만세 운동이 일어났어요. 저도 친구들과 함께 거리로 나가 \'대한독립 만세\'를 목이 터지도록 외쳤지요. 그때 처음으로 \'나도 나라를 위해 무언가 할 수 있구나\' 하고 느꼈어요.',
          },
          {
            keywords: ['아우내', '장터', '병천', '4월 1일', '4.1', '시위'],
            answer: '학교가 문을 닫자 고향 천안으로 내려와 마을 어른들과 함께 만세 시위를 준비했어요. 1919년 4월 1일 아우내 장터에서 약 삼천 명이 모여 \'대한독립 만세\'를 외쳤습니다. 미리 밤새 만든 태극기를 한 장 한 장 사람들에게 나눠드렸어요.',
          },
          {
            keywords: ['부모', '아버지', '어머니', '가족', '돌아가', '총'],
            answer: '아우내 장터에서 만세를 외치던 날, 일본 헌병의 총에 아버지와 어머니께서 함께 돌아가셨어요. 너무 슬프고 무서웠지만, 부모님이 끝까지 외치던 그 만세 소리를 멈출 수가 없었어요.',
          },
          {
            keywords: ['감옥', '서대문', '형무소', '갇히', '잡혀'],
            answer: '서대문 형무소 8호 감방에 갇혔어요. 좁고 차가운 곳이었지만, 매년 3월 1일이 되면 감방 안에서도 만세를 외쳤어요. 다른 방의 언니, 동지들이 함께 외쳐주실 때면 힘이 났답니다.',
          },
          {
            keywords: ['고문', '아파', '맞', '괴롭', '죽음', '순국', '돌아가'],
            answer: '감옥 안에서 모진 고문을 받았어요. 하지만 끝까지 \'대한독립 만세\'를 외쳤지요. 1920년 9월 28일, 열여덟 살의 나이로 옥중에서 세상을 떠났습니다. 두려웠지만 후회하지는 않아요.',
          },
          {
            keywords: ['태극기', '깃발', '그리', '만들'],
            answer: '아우내 시위를 준비하면서 직접 태극기를 만들었어요. 천에 손으로 한 장 한 장 그렸지요. 그 태극기를 받아 든 어른들의 떨리는 손이 아직도 기억나요.',
          },
          {
            keywords: ['친구', '동지', '학우', '동무'],
            answer: '이화학당에는 저와 같은 마음을 가진 친구들이 많았어요. \'5인의 결사대\'를 만들어 함께 만세 운동에 나섰지요. 어린 학생들도 나라를 사랑할 수 있다는 걸 우리는 함께 보여주었어요.',
          },
          {
            keywords: ['어린이', '학생', '후손', '또래', '하고 싶은', '바라', '미래'],
            answer: '여러분이 어리다고 해서 할 수 있는 일이 없다고 생각하지 마세요. 저도 열일곱 살이었어요. 작은 용기가 모이면 큰 힘이 됩니다. 그리고 친구들과 손잡는 일을 두려워하지 마세요.',
          },
        ],
        fallback: '그 이야기는 제가 잘 모르겠어요. 제 학교 이야기, 아우내 장터, 가족, 감옥 생활 같은 걸 물어봐 주시면 더 자세히 들려드릴게요.',
      },

      kim: {
        id: 'kim',
        name: '김구',
        years: '1876 ~ 1949',
        title: '대한민국 임시정부 주석',
        intro: '반갑소이다. 나는 백범 김구요. 우리 어린 학생들이 궁금한 것을 마음껏 물어봐 주시오.',
        color: 'bg-sky-100 text-sky-900',
        initial: '金',
        suggested: [
          '어디서 태어나셨나요?',
          '\'백범\'이라는 호에는 어떤 뜻이 담겨 있나요?',
          '임시정부는 어떤 곳인가요?',
          '한인애국단은 왜 만드셨나요?',
          '\'나의 소원\'은 무엇이었나요?',
        ],
        qa: [
          {
            keywords: ['태어', '출생', '고향', '어디서', '해주', '황해'],
            answer: '나는 1876년 황해도 해주에서 태어났소. 집안은 가난한 농가였소. 어릴 때 양반들에게 무시당하던 일이 마음에 사무쳐, "이 나라의 평범한 백성도 사람답게 살아야 한다"는 생각을 일찍 품게 되었지요.',
          },
          {
            keywords: ['백범', '호', '이름'],
            answer: '내 호는 \'백범(白凡)\'이오. \'백정\'의 백, \'범부(평범한 사람)\'의 범이라는 뜻이오. 우리나라에서 가장 천하다는 백정과 가장 평범한 백성까지도 모두 나라를 사랑할 줄 알게 되어야 진짜 독립이라고 생각했기 때문이오.',
          },
          {
            keywords: ['임시정부', '임정', '상하이', '주석', '충칭'],
            answer: '대한민국 임시정부는 1919년 상하이에서 세워진, 나라 잃은 우리 민족의 정부였소. 나는 그곳에서 경무국장, 내무총장을 거쳐 주석을 맡았소. 일본에 쫓겨 상하이에서 항저우, 충칭까지 옮겨다니며 일했지요.',
          },
          {
            keywords: ['한인애국단', '애국단', '이봉창', '윤봉길'],
            answer: '1931년에 한인애국단을 만들었소. 작은 조직이지만 큰 일을 해냈지요. 이봉창 의사가 1932년 도쿄에서 일본 천황에게 폭탄을 던졌고, 같은 해 윤봉길 의사가 상하이 훙커우 공원에서 의거를 성공시켰소. 두 분 모두 내 아우 같은 동지였소.',
          },
          {
            keywords: ['동학', '농민', '척사'],
            answer: '열여덟 살 때 동학에 들어가, 황해도에서 농민들과 함께 부패한 관리에 맞서 싸웠소. 비록 실패했지만, 그때부터 나는 \'우리 백성을 살리는 길이 무엇인가\'를 평생의 화두로 삼게 되었소.',
          },
          {
            keywords: ['광복', '해방', '1945', '8.15', '돌아'],
            answer: '1945년 8월 15일, 광복 소식을 들었을 때 기쁘기도 했지만 마음은 무거웠소. 우리 임시정부 군대가 직접 일본을 무찌르고 입성하려던 계획이 며칠 차이로 늦어졌기 때문이오. 그래서 광복 후 나라가 둘로 갈라지는 일을 막지 못한 것이 가장 아쉽소.',
          },
          {
            keywords: ['통일', '남북', '38선', '평양', '갈라'],
            answer: '광복 후 우리나라가 38선을 사이에 두고 둘로 갈라질 위기에 놓였소. 나는 1948년 38선을 넘어 평양에 다녀왔지요. "삼천만 동포에게 읍고함"이라는 글로 호소했소. 하나된 나라를 만드는 일이 무엇보다 중요했으니까.',
          },
          {
            keywords: ['죽음', '암살', '안두희', '경교장', '돌아가'],
            answer: '1949년 6월 26일, 서울 경교장에서 안두희라는 사람이 쏜 총에 맞아 세상을 떠났소. 일흔셋이었지요. 통일된 나라를 보지 못하고 떠난 것이 가장 큰 한이오.',
          },
          {
            keywords: ['나의 소원', '소원', '문화', '꿈꾸', '바라', '미래'],
            answer: '내가 책 "백범일지"에 쓴 "나의 소원"은 이렇소. 첫째도, 둘째도, 셋째도 우리나라의 \'완전한 자주독립\'이오. 그리고 무력보다 \'높은 문화의 힘\'을 가진 나라가 되기를 바랐소. 우리가 남을 행복하게 하는 나라가 되었으면 하오.',
          },
          {
            keywords: ['어린이', '학생', '후손', '하고 싶은'],
            answer: '여러분이 우리말과 글, 우리 노래와 이야기를 사랑해주시오. 그리고 다른 사람을 미워하기보다 행복하게 해주는 사람이 되어주시오. 그것이 진짜 강한 나라요.',
          },
        ],
        fallback: '허허, 그 부분은 조금 더 구체적으로 물어봐 주시겠소? 내 고향, 임시정부, 한인애국단, 나의 소원 같은 주제라면 자세히 이야기해 주리다.',
      },

      yun: {
        id: 'yun',
        name: '윤봉길',
        years: '1908 ~ 1932',
        title: '훙커우 공원의 청년',
        intro: '안녕하십니까. 저는 윤봉길입니다. 농촌의 청년이자 대한 독립을 꿈꾼 사람이었지요. 무엇이 궁금하신가요?',
        color: 'bg-emerald-100 text-emerald-900',
        initial: '尹',
        suggested: [
          '어디서 태어나셨나요?',
          '왜 농촌 야학을 여셨나요?',
          '훙커우 공원 의거 이야기를 들려주세요',
          '거사 전날 김구 선생님과 무슨 일이 있었나요?',
          '두 아들에게 어떤 편지를 남기셨나요?',
        ],
        qa: [
          {
            keywords: ['태어', '출생', '고향', '어디서', '예산', '덕산', '충청'],
            answer: '저는 1908년 충청남도 예산군 덕산면에서 태어났습니다. 농촌의 평범한 집안이었지요. 어릴 때부터 한학을 공부했고, 글 읽기를 좋아했습니다.',
          },
          {
            keywords: ['야학', '농촌', '계몽', '농민', '가르치', '교육'],
            answer: '고향에서 \'부흥원\'이라는 야학을 열어 농민들과 아이들을 가르쳤습니다. 농민이 깨어나야 나라가 산다고 믿었기 때문입니다. 한글을 모르는 어르신들과 함께 글공부를 하던 시간이 보람찼지요.',
          },
          {
            keywords: ['농민독본', '책', '글'],
            answer: '농민들을 위한 책 "농민독본"을 직접 썼습니다. 우리말과 셈하기, 농사 지식과 함께 \'나라란 무엇인가\'를 풀어 썼지요. 책 한 권으로라도 마을이 바뀌기를 바랐습니다.',
          },
          {
            keywords: ['출가', '집을 떠', '상하이로', '망명', '장부'],
            answer: '1930년, 스물세 살에 고향을 떠나며 "장부출가생불환(丈夫出家生不還)" — 대장부가 집을 나서면 살아 돌아오지 않는다 — 이렇게 다짐했습니다. 그 길로 만주를 거쳐 상하이로 갔지요.',
          },
          {
            keywords: ['훙커우', '홍커우', '상하이', '의거', '거사', '폭탄', '도시락', '물통', '4월 29일', '4.29'],
            answer: '1932년 4월 29일, 상하이 훙커우 공원에서 일본이 천황의 생일과 상하이 점령을 축하하는 행사를 열었습니다. 저는 물통 모양의 폭탄을 단상에 던졌고, 일본군 사령관 시라카와 대장과 여러 일본 수뇌부가 죽거나 다쳤습니다. 그 자리에서 저는 \'대한 독립 만세\'를 외쳤습니다.',
          },
          {
            keywords: ['김구', '백범', '시계', '바꿔', '아침', '전날'],
            answer: '거사를 앞두고 김구 선생님과 마지막 식사를 했습니다. 그 자리에서 저는 제 새 시계를, 선생님은 헌 시계를 서로 바꾸어 찼지요. "이제 한 시간 후면 저는 이 시계가 필요 없습니다"라고 말씀드렸습니다. 마음이 흔들리지 않았다면 거짓말일 테지만, 후회는 없었습니다.',
          },
          {
            keywords: ['아들', '편지', '유서', '두 아들', '자식', '아내'],
            answer: '두 아들 모순과 담에게 편지를 남겼습니다. "너희도 만일 피가 있고 뼈가 있다면 반드시 조선을 위하여 용감한 투사가 되어라." 어린 아들들을 두고 떠나는 아비의 마음은 무거웠지만, 그래도 그 길이 옳다고 믿었습니다.',
          },
          {
            keywords: ['죽음', '사형', '순국', '총살', '돌아가', '가나자와'],
            answer: '거사 후 일본으로 끌려가 1932년 12월 19일 가나자와에서 총살되었습니다. 그때 제 나이 스물넷이었습니다. 두 손과 두 발이 묶인 채였지만, 저는 끝까지 떳떳했습니다.',
          },
          {
            keywords: ['임시정부', '한인애국단', '단원'],
            answer: '저는 한인애국단의 단원이었습니다. 김구 선생님이 이끄시던 그 작은 조직이 임시정부의 손과 발 같은 역할을 했지요. 저는 마지막에 태극기 앞에서 \'한인애국단원으로서 조국의 독립과 자유를 회복하기 위하여\' 라는 선서문을 읽고 서명했습니다.',
          },
          {
            keywords: ['어린이', '학생', '후손', '하고 싶은', '바라'],
            answer: '젊다고 해서 할 수 있는 일이 없다고 생각하지 마십시오. 저도 스무 살이 갓 넘은 청년이었습니다. 한 사람의 결심이 역사를 바꿀 수 있습니다. 그 결심을 무엇에 쓰느냐는 여러분께 달려 있습니다.',
          },
        ],
        fallback: '음, 그 부분은 제가 잘 답변드릴 수 있는 주제가 아닌 듯합니다. 제 고향, 야학, 훙커우 의거, 김구 선생님과의 일 같은 걸 물어봐 주시면 자세히 말씀드리겠습니다.',
      },
    };

    // ====== 상태 ======
    let currentPerson = null;
    let messages = [];

    // ====== 매칭 로직 ======
    function findAnswer(person, question) {
      const q = question.toLowerCase().replace(/\s+/g, '');
      let best = null;
      let bestScore = 0;
      for (const qa of person.qa) {
        let score = 0;
        for (const kw of qa.keywords) {
          const k = kw.toLowerCase().replace(/\s+/g, '');
          if (q.includes(k)) {
            score += k.length;
          }
        }
        if (score > bestScore) {
          bestScore = score;
          best = qa;
        }
      }
      return best ? best.answer : person.fallback;
    }

    // ====== 렌더링 ======
    const app = document.getElementById('app');

    function renderHome() {
      currentPerson = null;
      messages = [];
      app.innerHTML = `
        <div class="min-h-screen bg-gray-50 px-5 py-10">
          <div class="max-w-3xl mx-auto">
            <header class="mb-10">
              <p class="text-base font-medium text-teal-700 mb-2">역사 인물 인터뷰</p>
              <h1 class="text-3xl sm:text-4xl font-bold text-gray-900 leading-tight">
                독립운동가와 이야기 나눠보기
              </h1>
              <p class="mt-4 text-lg text-gray-600 leading-relaxed">
                만나보고 싶은 인물을 한 분 골라 보세요. 직접 질문하며 인터뷰할 수 있어요.
              </p>
            </header>

            <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
              ${Object.values(PEOPLE).map(p => `
                <button
                  data-person="${p.id}"
                  class="person-card group text-left bg-white border border-gray-200 rounded-2xl p-6 hover:border-teal-500 hover:shadow-sm transition-all focus:outline-none focus:ring-2 focus:ring-teal-500"
                >
                  <div class="flex items-start gap-4">
                    <div class="w-14 h-14 ${p.color} rounded-2xl flex items-center justify-center text-2xl font-bold flex-shrink-0">
                      ${p.initial}
                    </div>
                    <div class="flex-1 min-w-0">
                      <h2 class="text-xl font-bold text-gray-900">${p.name}</h2>
                      <p class="text-sm text-gray-500 mt-1">${p.years}</p>
                      <p class="text-base text-gray-700 mt-3 leading-relaxed">${p.title}</p>
                    </div>
                  </div>
                </button>
              `).join('')}
            </div>

            <section class="mt-10 bg-white rounded-2xl p-6 border border-gray-200">
              <h3 class="text-lg font-bold text-gray-900 mb-3">인터뷰하기 전에</h3>
              <ul class="space-y-2 text-base text-gray-700 leading-relaxed">
                <li>· 인물의 입장에서 답변해 드려요. 정확한 사실을 바탕으로 한 답이지만, 실제 그분의 말 그대로는 아니에요.</li>
                <li>· 한 번에 한 가지 주제를 물어보면 더 좋은 답을 들을 수 있어요.</li>
                <li>· 추천 질문 외에도 자유롭게 직접 질문을 적어보세요.</li>
              </ul>
            </section>
          </div>
        </div>
      `;

      document.querySelectorAll('.person-card').forEach(btn => {
        btn.addEventListener('click', () => {
          const id = btn.dataset.person;
          startInterview(PEOPLE[id]);
        });
      });
    }

    function startInterview(person) {
      currentPerson = person;
      messages = [{ role: 'them', text: person.intro }];
      renderChat();
    }

    function renderChat() {
      const p = currentPerson;
      app.innerHTML = `
        <div class="min-h-screen flex flex-col bg-gray-50">
          <header class="bg-white border-b border-gray-200 sticky top-0 z-10">
            <div class="max-w-3xl mx-auto px-5 py-4 flex items-center gap-4">
              <button id="backBtn" aria-label="인물 선택으로 돌아가기" class="w-11 h-11 rounded-xl hover:bg-gray-100 flex items-center justify-center focus:outline-none focus:ring-2 focus:ring-teal-500">
                <svg width="22" height="22" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.2" stroke-linecap="round" stroke-linejoin="round" class="text-gray-700">
                  <path d="M19 12H5"/><path d="M12 19l-7-7 7-7"/>
                </svg>
              </button>
              <div class="w-11 h-11 ${p.color} rounded-xl flex items-center justify-center text-lg font-bold flex-shrink-0">
                ${p.initial}
              </div>
              <div class="flex-1 min-w-0">
                <h1 class="text-lg font-bold text-gray-900 truncate">${p.name}</h1>
                <p class="text-sm text-gray-500 truncate">${p.years} · ${p.title}</p>
              </div>
            </div>
          </header>

          <main id="chatArea" class="chat-scroll flex-1 overflow-y-auto">
            <div class="max-w-3xl mx-auto px-5 py-6 space-y-4" id="messages"></div>
          </main>

          <div class="bg-white border-t border-gray-200">
            <div class="max-w-3xl mx-auto px-5 py-4">
              <div id="suggestedRow" class="flex gap-2 overflow-x-auto pb-3 -mx-1 px-1"></div>
              <form id="askForm" class="flex gap-2 items-end">
                <label for="askInput" class="sr-only">질문 입력</label>
                <textarea
                  id="askInput"
                  rows="1"
                  placeholder="궁금한 점을 적어보세요"
                  class="flex-1 resize-none py-3 px-4 rounded-2xl border border-gray-200 focus:ring-2 focus:ring-teal-500 focus:border-transparent text-lg leading-relaxed bg-gray-50 focus:bg-white outline-none"
                ></textarea>
                <button
                  type="submit"
                  id="sendBtn"
                  aria-label="질문 보내기"
                  class="w-12 h-12 bg-teal-600 hover:bg-teal-700 disabled:bg-gray-300 text-white rounded-2xl flex items-center justify-center flex-shrink-0 focus:outline-none focus:ring-2 focus:ring-teal-500 transition-colors"
                >
                  <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.4" stroke-linecap="round" stroke-linejoin="round">
                    <path d="M12 19V5"/><path d="M5 12l7-7 7 7"/>
                  </svg>
                </button>
              </form>
            </div>
          </div>
        </div>
      `;

      renderMessages();
      renderSuggested();

      document.getElementById('backBtn').addEventListener('click', renderHome);

      const form = document.getElementById('askForm');
      const input = document.getElementById('askInput');

      input.addEventListener('input', () => {
        input.style.height = 'auto';
        input.style.height = Math.min(input.scrollHeight, 140) + 'px';
      });

      input.addEventListener('keydown', (e) => {
        if (e.key === 'Enter' && !e.shiftKey) {
          e.preventDefault();
          form.requestSubmit();
        }
      });

      form.addEventListener('submit', (e) => {
        e.preventDefault();
        const text = input.value.trim();
        if (!text) return;
        sendQuestion(text);
        input.value = '';
        input.style.height = 'auto';
      });

      input.focus();
    }

    function renderMessages() {
      const list = document.getElementById('messages');
      if (!list) return;
      list.innerHTML = messages.map(m => {
        if (m.role === 'them') {
          return `
            <div class="flex items-start gap-3 bubble-in">
              <div class="w-10 h-10 ${currentPerson.color} rounded-xl flex items-center justify-center text-base font-bold flex-shrink-0">
                ${currentPerson.initial}
              </div>
              <div class="max-w-[80%] bg-white border border-gray-200 rounded-2xl rounded-tl-md px-5 py-3 text-lg text-gray-900 leading-relaxed whitespace-pre-line">
                ${escapeHtml(m.text)}
              </div>
            </div>
          `;
        }
        if (m.role === 'typing') {
          return `
            <div class="flex items-start gap-3 bubble-in">
              <div class="w-10 h-10 ${currentPerson.color} rounded-xl flex items-center justify-center text-base font-bold flex-shrink-0">
                ${currentPerson.initial}
              </div>
              <div class="bg-white border border-gray-200 rounded-2xl rounded-tl-md px-5 py-4 flex items-center gap-1.5">
                <span class="dot w-2 h-2 bg-gray-400 rounded-full inline-block"></span>
                <span class="dot w-2 h-2 bg-gray-400 rounded-full inline-block"></span>
                <span class="dot w-2 h-2 bg-gray-400 rounded-full inline-block"></span>
              </div>
            </div>
          `;
        }
        return `
          <div class="flex justify-end bubble-in">
            <div class="max-w-[80%] bg-teal-600 text-white rounded-2xl rounded-tr-md px-5 py-3 text-lg leading-relaxed whitespace-pre-line">
              ${escapeHtml(m.text)}
            </div>
          </div>
        `;
      }).join('');

      const area = document.getElementById('chatArea');
      if (area) {
        requestAnimationFrame(() => {
          area.scrollTop = area.scrollHeight;
        });
      }
    }

    function renderSuggested() {
      const row = document.getElementById('suggestedRow');
      if (!row) return;
      row.innerHTML = currentPerson.suggested.map(q => `
        <button class="suggested-chip flex-shrink-0 px-4 py-2 bg-gray-100 hover:bg-gray-200 text-gray-800 rounded-full text-base whitespace-nowrap focus:outline-none focus:ring-2 focus:ring-teal-500 transition-colors">
          ${escapeHtml(q)}
        </button>
      `).join('');

      row.querySelectorAll('.suggested-chip').forEach((btn, i) => {
        btn.addEventListener('click', () => {
          sendQuestion(currentPerson.suggested[i]);
        });
      });
    }

    function sendQuestion(text) {
      messages.push({ role: 'me', text });
      messages.push({ role: 'typing' });
      renderMessages();

      const delay = 600 + Math.min(text.length * 12, 800);
      setTimeout(() => {
        messages = messages.filter(m => m.role !== 'typing');
        const answer = findAnswer(currentPerson, text);
        messages.push({ role: 'them', text: answer });
        renderMessages();
      }, delay);
    }

    function escapeHtml(s) {
      return s
        .replace(/&/g, '&amp;')
        .replace(/</g, '&lt;')
        .replace(/>/g, '&gt;')
        .replace(/"/g, '&quot;')
        .replace(/'/g, '&#039;');
    }

    renderHome();
  </script>
</body>
</html>
