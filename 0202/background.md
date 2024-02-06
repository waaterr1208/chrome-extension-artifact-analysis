# backgroung.js 코드 해석
```
var showContextMenu = undefined;

// 확장 프로그램 설정이 변경될 때마다 호출되는 콜백 함수
updateCallback = function () {
    if (showContextMenu !== preferences.showContextMenu) {
        showContextMenu = preferences.showContextMenu;
        setContextMenu(showContextMenu);
    }
    setHolidayIcon();
};

// 초기화 작업
setHolidayIcon();
setInterval(setHolidayIcon, 60 * 60 * 1000); // 매 1시간마다 실행

// 브라우저가 재시작될 때 사용자가 옵션 페이지에 처음 접근하면 기본 페이지(지원 페이지)로 이동
localStorage.setItem("option_panel", "null");

// 현재 버전 및 이전 버전 확인
var currentVersion = chrome.runtime.getManifest().version;
var oldVersion = data.lastVersionRun;
data.lastVersionRun = currentVersion;

// 확장 프로그램 업데이트 확인 및 처리
if (false && oldVersion !== currentVersion) {
    if (oldVersion === undefined) { // 처음 실행
        chrome.tabs.create({ url: 'http://www.editthiscookie.com/start/' });
    } else {
        // 알림 생성 및 클릭 시 변경 내용 페이지로 이동
        chrome.notifications.onClicked.addListener(function (notificationId) {
            chrome.tabs.create({
                url: 'http://www.editthiscookie.com/changelog/'
            });
            chrome.notifications.clear(notificationId, function (wasCleared) { });
        });

        // 알림 옵션 설정
        var opt = {
            type: "basic",
            title: "EditThisCookie",
            message: _getMessage("updated"),
            iconUrl: "/img/icon_128x128.png",
            isClickable: true
        };

        // 알림 생성
        chrome.notifications.create("", opt, function (notificationId) {
        });
    }
}

// 쿠키 변경 이벤트에 대한 리스너 등록
chrome.cookies.onChanged.addListener(function (changeInfo) {
    // ... (쿠키 변경에 따른 동작 처리)
});

// SET-COOKIE 헤더를 삭제하는 리스너 등록
chrome.webRequest.onHeadersReceived.addListener(
    function(details) {
        // ... (SET-COOKIE 헤더 처리)
    },
    {urls: ["<all_urls>"]},
    ["blocking", "responseHeaders", "extraHeaders"]
);

// 콘텍스트 메뉴 설정 함수
function setContextMenu(show) {
    chrome.contextMenus.removeAll();
    if (show) {
        chrome.contextMenus.create({
            "title": "EditThisCookie",
            "contexts": ["page"],
            "onclick": function (info, tab) {
                showPopup(info, tab);
            }
        });
    }
}

// 휴일 아이콘 설정 함수
function setHolidayIcon() {
    if (isChristmasPeriod() && preferences.showChristmasIcon) {
        chrome.browserAction.setIcon({ "path": "/img/cookie_xmas_19x19.png" });
    } else {
        chrome.browserAction.setIcon({ "path": "/img/icon_19x19.png" });
    }
}
```
