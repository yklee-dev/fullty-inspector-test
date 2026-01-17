<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0"> <title>풀티 원스톱 검수 시스템</title>
    <style>
        body { font-family: 'Pretendard', sans-serif; padding: 20px; background-color: #f4f4f4; }
        .container { max-width: 600px; margin: 0 auto; background: white; padding: 20px; border-radius: 10px; box-shadow: 0 2px 10px rgba(0,0,0,0.1); }
        input, select, textarea { width: 100%; padding: 12px; margin-bottom: 15px; border: 1px solid #ddd; border-radius: 5px; box-sizing: border-box; font-size: 16px; }
        label { font-weight: bold; display: block; margin-bottom: 5px; }
        
        /* 카메라 버튼 스타일 */
        .file-upload { display: block; background: #333; color: white; text-align: center; padding: 15px; border-radius: 5px; cursor: pointer; margin-bottom: 15px; }
        #preview { width: 100%; max-height: 300px; object-fit: contain; display: none; margin-bottom: 15px; }
        
        /* 제출 버튼 */
        button { width: 100%; background-color: #FF5A5F; color: white; padding: 15px; border: none; border-radius: 5px; font-size: 18px; font-weight: bold; cursor: pointer; }
        button:disabled { background-color: #ccc; }
    </style>
</head>
<body>

<div class="container">
    <h2>🧐 풀티 제품 검수</h2>
    
    <label>제품명</label>
    <input type="text" id="productName" placeholder="예: 허먼밀러 넬슨 벤치">

    <label>등급 선택</label>
    <select id="grade">
        <option value="S">S등급 (미사용/전시)</option>
        <option value="A">A등급 (미세 스크래치)</option>
        <option value="B">B등급 (사용감 있음)</option>
    </select>

    <label>하자 사진 촬영 (클릭)</label>
    <label for="cameraInput" class="file-upload">📷 카메라 켜기 / 사진 선택</label>
    <input type="file" id="cameraInput" accept="image/*" capture="environment" style="display:none;" onchange="previewImage()">
    <img id="preview" src="">

    <label>특이사항 (메모)</label>
    <textarea id="memo" rows="3" placeholder="스크래치 위치 등 상세 내용"></textarea>

    <button id="submitBtn" onclick="submitData()">검수 완료 및 저장</button>
</div>

<script>
    let base64Image = "";
    let fileName = "";
    let mimeType = "";

    // 1. 이미지 미리보기 및 데이터 변환 함수
    function previewImage() {
        const file = document.querySelector("#cameraInput").files[0];
        if (file) {
            fileName = file.name;
            mimeType = file.type;
            const reader = new FileReader();
            reader.onload = function(e) {
                document.getElementById("preview").src = e.target.result;
                document.getElementById("preview").style.display = "block";
                // "data:image/jpeg;base64,..." 형태에서 실제 데이터만 분리
                base64Image = e.target.result.split(',')[1]; 
            };
            reader.readAsDataURL(file);
        }
    }

    // 2. 데이터 전송 함수
    function submitData() {
        const btn = document.getElementById("submitBtn");
        
        // 유효성 검사
        if(!base64Image) { alert("사진을 찍어주세요!"); return; }
        
        btn.disabled = true;
        btn.innerText = "저장 중입니다... (잠시만요)";

        const data = {
            productName: document.getElementById("productName").value,
            grade: document.getElementById("grade").value,
            memo: document.getElementById("memo").value,
            image: base64Image,
            fileName: fileName,
            mimeType: mimeType
        };

        // ★ 아까 복사한 '웹 앱 URL'을 아래 따옴표 안에 넣으세요
        const SCRIPT_URL = "여기에_아까_복사한_웹앱_URL을_넣으세요"; 

        fetch(SCRIPT_URL, {
            method: "POST",
            mode: "no-cors", // 중요: 구글 스크립트로 보낼 때 필수
            headers: { "Content-Type": "application/json" },
            body: JSON.stringify(data)
        })
        .then(() => {
            alert("✅ 저장 완료! 구글 시트에 등록되었습니다.");
            window.location.reload(); // 새로고침
        })
        .catch(error => {
            alert("오류 발생: " + error);
            btn.disabled = false;
            btn.innerText = "검수 완료 및 저장";
        });
    }
</script>

</body>
</html>
