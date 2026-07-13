# v0.2.0-alpha.1 — 첫 대사 한글 출력

게임 시작 후 소피아의 첫 대사 메시지 ID 5를 한국어로 교체하는 알파 패치입니다.
원문 메시지의 말하는 사람, 줄바꿈, 대기·타이밍·스타일 제어 순서를 보존했습니다.

## 주요 변경

- archive 8의 전역 24×24 폰트에 한글 14자 추가
- OFL의 Noto Sans CJK KR Regular 사용, 22px·4단계 명암
- 후속 DTT stream을 같은 archive 안에서 이동하고 ZLS 링크 복구
- archive 1220의 장면 폰트 local base `301 → 307`
- 기존 local glyph operand 108개 전부 `+6` remap, bitmap 72개 불변
- 두 이름 호출을 한국어 literal glyph로 치환하고 메시지 제어열 보존
- 공개 패처, 독립 검증기, 합성 테스트, 정적 미리보기 추가

## 지원 및 해시

- 원본 ISO SHA-256:
  `95CC4E25AC71DE7C6263AA2E544910DE30667EA3BA62726CF4A019F24B038826`
- 출력 ISO SHA-256:
  `BBC366A55DA0C2C985BB7E5329A7D2FE8674913995EEB4C81E9674C1E42AF27C`
- 릴리스 ZIP SHA-256:
  `1B7C7E48F440D7F2B82CD65D0E31F025ED6F472FBBE83DE69922629875237893`
- xdelta SHA-256:
  `D0F053E5972D7F1E4C045D706DE028C00513DC814641A9A093BFAB3571C2788B`
- Noto Sans CJK KR Regular SHA-256:
  `6BCB2A0703AA137E874FC2DFFA85F6C21BA9A67FA329E81B8C801663AF7E992A`

## 검증

- 4.69GB 전체 diff: 허용 archive 밖 변경 `0` bytes
- archive 1220 첫 PK1 records: `12` 검사, `11` 불변, `1` 목표 변경
- 전체 추출 manifest root SLZ streams: `63` 검사, `62` 불변, `1` 목표 변경
- xdelta encode/decode 왕복 후 출력 ISO 해시 일치

완성 한글패치가 아니라 첫 대사를 대상으로 한 구조·출력 검증판입니다. 게임 파일,
BIOS, 실행 파일, 추출 리소스, 폰트 파일은 배포하지 않습니다.
