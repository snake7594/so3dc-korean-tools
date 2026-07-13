# SO3 DC Korean Text POC – First mclib Patch

Star Ocean 3 Director's Cut 일본판 Disc 1의 숨겨진 tri-Ace 아카이브, SLZ
mode 2, `so3mclib` 메시지/폰트 구조를 분석하고 첫 크레딧 텍스트
`Game Designer`를 `게임 디자이너`로 재구성하는 초기 도구와 xdelta입니다.

## 포함

- 숨겨진 6,144-entry 아카이브 인덱스 해독 및 추출기
- SLZ mode 2 압축/해제
- 24×24/32×32 mclib 글리프 및 메시지 처리
- 원본을 수정하지 않고 별도 ISO를 만드는 안전 검사
- 첫 텍스트 POC용 xdelta

## 검증

- 지원 원본 ISO SHA-256:
  `95CC4E25AC71DE7C6263AA2E544910DE30667EA3BA62726CF4A019F24B038826`
- 출력 ISO SHA-256:
  `3729A58B52DA7E0458F7B9E3B23CDAB0C102547B3DA4EC2028BEA2E597485176`
- 대상: archive 66 / stream 57 / message 10
- 변경 범위가 선택한 SLZ member 내부로 제한됨
- 다음 chained member 64 KiB가 원본과 동일함
- PCSX2 냉부팅 후 타이틀 화면까지 진행됨

## 제한

- 완성 한글패치가 아닌 첫 텍스트 출력 POC
- 현재 reuse 방식은 같은 32px atlas의 기존 글리프 6개를 교체하므로 이후
  메시지에 영향을 줄 수 있음
- 스트림 재배치 미지원
- 목표 한국어 카드의 최종 런타임 화면 캡처는 아직 진행 중

게임 ISO, 실행 파일, 추출 데이터, BIOS, 폰트 파일은 포함하지 않습니다.
사용자가 적법하게 보유한 일치하는 원본 Disc 1 ISO가 필요합니다.
