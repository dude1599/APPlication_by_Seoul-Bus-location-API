# APPlication_by_Seoul-Bus-location-API
## This is a code that using API for bus location information inquiry service in Seoul.

### 서울특별시_버스위치정보조회 서비스 API를 이용해 버스정보를 실시간으로 로컬 웹페이지에서 조회해보는 예제입니다.

아래에서 소개할 코드들은 <https://www.data.go.kr/> : [공공데이터 포털](https://www.data.go.kr/) 중 서울특별시_버스 위치 정보 조회 서비스 예제 코드를 참조했습니다.

----
<img src="https://img.shields.io/badge/Node.js-green?logo=Node.js&logoColor=black" alt="image" width="230" height="50"/>                     

v20.9.0


<img src="https://img.shields.io/badge/VS%20Code-blue?logo=visualstudiocode&logoColor=purple" alt="image2" width="230" height="50"/>         

v1.84.2


---
## !Description!
이 레포지토리는 서울특별시 버스 위치 정보 조회 서비스 API를 활용하여 웹 서버에 API 호출 결과를 표시하는 코드에 대한 설명입니다.

이 프로젝트는 Node.js와 VSCODE를 사용하여 개발되었습니다. Node.js는 공공데이터포털에 제공된 예제 코드를 기반으로 하였습니다.

HTML 코드는 기존의 API 호출 결과에 'Get Bus Information' 버튼을 추가하여, 로컬 웹 서버에 접속 시 프로젝트 이름과 API 호출 결과를 명확히 확인할 수 있도록 구성되었습니다.

뿐만 아니라, API 호출 결과의 태그들이 영어로 되어 있어 결과를 정확히 이해하는 데 어려움이 있어, 이를 고려하여 필수적인 태그에 대한 설명을 문자열로 추가하여 이해를 돕도록 했습니다.

---
This repository provides an explanation of the code that utilizes the Seoul Special City Bus Location Information Service API to display API call results on a web server. The project was developed using Node.js and VSCODE, with Node.js based on example code provided by the Public Data Portal.

The HTML code is structured to enhance clarity by adding a 'Get Bus Information' button to the existing API call results. This allows users to easily confirm the project name and API call results when accessing the local web server.

Furthermore, considering the difficulty in understanding the API call result tags written in English, descriptions for essential tags have been added as strings to facilitate comprehension.
        
---
## API를 로컬 웹 서버에서 실행 시 결과 (최종 결과)
---
![스크린샷 2023-11-19 180250](https://github.com/dude1599/APPlication_by_Seoul-Bus-location-API/assets/133233495/139bde92-ee63-4fa6-99d0-11b80d328cb1)

---
## Node.Js Code

### <index.js>
```
const express = require('express');
const app = express();
const request = require('request');

app.use(express.static("public_html"));

app.listen(8080, function () {
    console.log("서버 시작됨");
});

app.get('/getBusApi', function (req, res) {
    var request = require('request');

    var url = 'http://ws.bus.go.kr/api/rest/buspos/getBusPosByRouteSt';
    var queryParams = '?' + encodeURIComponent('serviceKey') + '=본인 서비스 키 입력'; /*Service Key*/
    queryParams += '&' + encodeURIComponent('busRouteId') + '=' + encodeURIComponent('100100232'); /*노선ID*/
    queryParams += '&' + encodeURIComponent('startOrd') + '=' + encodeURIComponent('1'); /*시작 정류소 순번*/
    queryParams += '&' + encodeURIComponent('endOrd') + '=' + encodeURIComponent('10'); /*종료 정류소 순번*/

    request({
        url: url + queryParams,
        method: 'GET'
    }, function (error, response, body) {
        console.log('Status', response.statusCode);
        console.log('Headers', JSON.stringify(response.headers));
        console.log('Reponse received', body);
        
        // 클라이언트에게 결과 전송
        res.send(body);
    });
});
```

## HTML CODE
### <index.html>
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Seoul_Bus Location Information</title>

    <style>
        #getBusBtn {
            font-size: 1.5em;           /* 크기 조정 */
            padding: 13px 26px;         /* 여백 조정 */
            margin-top: 10px;
        }
    </style>
</head>
<body>
    <h1>Project : Using Seoul_Bus Information API</h1>
    <button id="getBusBtn">Get Bus Information</button>
    <div id="Busdata"></div>

    <script>
        document.getElementById('getBusBtn').addEventListener('click', function () {
            fetch('/getBusApi')
                .then(response => response.text())
                .then(data => {
                    
                    const additionalInfo = `-------------------------------------------------------
"bustype : 버스 종류 (0=일반버스, 1 = 저상버스)"
"isFullFlag : 만차여부 (0 = 만차아님, 1 = 만차)"
"lastStnId : 최종 정류장 ID"
"stopFlag : 정류소 도착여부 (0 = 운행중, 1 = 도착)"
"tmx / tmy : 지도 좌표 (경도, 위도)"
"vehId : 버스 ID"
-------------------------------------------------------`;
                    // 줄 바꿈(행 띄우기)
                    const hr = '\n';

                    const formattedData = `${additionalInfo}${hr}${JSON.stringify(data, null, 2)}`;
                    document.getElementById('Busdata').innerText = formattedData;
                })
                .catch(error => console.error('Error:', error));
        });
    </script>
</body>
</html>
```
