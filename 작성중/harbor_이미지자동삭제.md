
사설 이미지 저장소를 찾다가, CNCF 프로젝트인 harbor를 선택하였었다.

만들어진 서비스나 툴을 사용하는것보다 직접 운영하는것이 신경쓸것이 많은것 같긴 하다.
사용하다가 이미지 용량을 너무 많이 차지 하면 안되니,
적정선에서 이미지를 지워주는 기능을 적는다.

----------------------



처음에는 프로젝트 메뉴로 와서, 적절한 프로젝트를 선택한다.
<img width="1677" alt="image" src="https://user-images.githubusercontent.com/23617635/209807862-5e67eb84-9078-4512-8d61-5e9c7d13acde.png">

들어왔다면, 서브 메뉴중에 policy 라는 메뉴를 선택한다.
<img width="1678" alt="image" src="https://user-images.githubusercontent.com/23617635/209807905-7682552f-5b31-4100-9adb-25a1c00cfc3f.png">

이러한 페이지가 나오는데, TAG RETENTION 토글을 선택하고,
ADD RULE 버튼을 누른다.
<img width="1680" alt="image" src="https://user-images.githubusercontent.com/23617635/209808103-a0caf8e1-8633-434a-bf1d-7453700fac54.png">

팝업창에서 룰을 생성하는데, 
첫번째 줄에서는 어떤 레지스트리에 적용할건지 또는 제외시킬건지
정규식으로 작성한다.
두번째 줄에서는 
가장 최근에 푸쉬된것을 Count 만큼 남길지, 
가장 최근에 풀 된것을 Count 만큼 남길지,
지난 몇일동안 푸쉬된걸 Count 만큼 남길지,
지난 몇일동안 풀한걸 Count 만큼 남길지
항상 유지할지. 
선택하고, 해당하는 Count를 숫자로 적는다.
세번째 줄에서는 태그 정규식을 적고, 적용할지, 제외시킬지 작성하는곳이다.
(또는 태그가 지정된것에 대한 유무를 체크박스로 체크한다)
<img width="1527" alt="image" src="https://user-images.githubusercontent.com/23617635/209808149-d6aa4b4c-68be-4a88-9dbd-84e11c9b9ead.png">
<img width="1123" alt="image" src="https://user-images.githubusercontent.com/23617635/209808557-d7215899-b54f-457d-9e59-ebad19c899bb.png">


자동으로 시간마다, 날마다, 주마다, 커스텀 으로 cron을 동작시킬수 있다.
또는 None으로 해서 수동으로 작동시켜도 된다.
<img width="1402" alt="image" src="https://user-images.githubusercontent.com/23617635/209808323-c3cd01aa-06ed-42ef-898e-504cb2542649.png">


3번은 Dry run을 클릭하여 몇개의 태그가 남고, 지워지는지 확인한것이고
4번은 실제로 Run Now 버튼을 눌러서 작동시킨것이다.
<img width="840" alt="image" src="https://user-images.githubusercontent.com/23617635/209808467-365a11e2-e108-4424-bd1e-c64095faa57e.png">


