# Star Ocean 3 DC Korean Tools

『스타 오션 3 Till the End of Time Director's Cut』 일본판 PS2 Disc 1의
숨겨진 tri-Ace 아카이브, SLZ 압축, `so3mclib` 메시지·폰트 형식을 분석하고
한국어 출력 패치를 만드는 연구 도구입니다.

`v0.4.0-alpha.1`은 게임 초반 하이다(Hyda) 구간의 24개 이벤트 뱅크에서 확인한
대사 653개를 한국어로 교체합니다. 바이트가 같은 대사를 하나로 묶으면 번역
레코드는 434개입니다. 이 수치는 게임 전체 대사 수가 아니라 이번 알파 릴리스의
검증된 적용 범위입니다.

한국어 글리프는 `NanumSquareNeo-cBd.ttf`로 생성합니다. 각 장면의 24×24 로컬
폰트에서 비목표 메시지가 참조하는 슬롯은 보호하고, 목표 전용 또는 미사용 슬롯을
먼저 재사용한 뒤 필요한 글리프만 추가합니다. 공개 입력은 일본어 원문이나 추출
게임 바이트를 담지 않는 구조 매니페스트와 한국어 JSON으로 분리되어 있습니다.

> `v0.4.0-alpha.1`은 전체 한글패치가 아닙니다. 정적 구조 검증과 ISO 차이 범위
> 검증을 위한 알파 릴리스이며, 에뮬레이터 런타임 검증은 의도적으로 수행하지
> 않았습니다. 실제 출력과 게임 진행은 사용자가 확인해야 합니다.

## 패치 범위

- 초반 하이다 이벤트 뱅크 `24`개
- 물리적 대사 위치 `653`개
- 동일 원문 바이트를 합친 한국어 번역 레코드 `434`개
- 화자 이름과 본문 한국어화
- 원문의 줄 수 및 검증된 이벤트·레이아웃 제어열 보존
- NanumSquare Neo Bold 기반 24×24, 22px, 2단계 명암 로컬 글리프

번역과 구조 입력은 다음 두 파일입니다.

- [`translations/hyda_ko.json`](translations/hyda_ko.json): 한국어 화자·본문과
  적용 위치 및 원문 세그먼트 SHA-256
- [`translations/hyda_patch_manifest.json`](translations/hyda_patch_manifest.json):
  원문 텍스트와 원문 바이트를 제외한 해시·오프셋 기반 구조 매니페스트

자세한 패치 구조는
[`docs/HYDA_653_KOREAN_PATCH.md`](docs/HYDA_653_KOREAN_PATCH.md)를 참고하세요.

## 지원 원본

- 게임: 일본판 Director's Cut Disc 1
- 제품 코드: `SLPM-65438`
- ISO 크기: `4,689,854,464` bytes
- 원본 ISO SHA-256:
  `95CC4E25AC71DE7C6263AA2E544910DE30667EA3BA62726CF4A019F24B038826`

해시가 다른 덤프에는 릴리스 xdelta를 적용하거나 패처를 실행하지 마세요. 패처와
검증기는 지원 크기·해시·구조가 다르면 처리를 중단합니다.

## v0.4.0-alpha.1 xdelta 적용

GitHub Releases에서
`SO3_DC_Disc1_Korean_Hyda_653_Nanum_v0.4.0-alpha.1.zip`을 받아 풀고 다음과
같이 적용합니다.

```powershell
xdelta3 -d -s "SO3_DC_Disc1_original.iso" `
  "SO3_DC_Disc1_Korean_Hyda_653_Nanum_v0.4.0-alpha.1.xdelta" `
  "SO3_DC_Disc1_Korean_Hyda_653_Nanum.iso"
```

반드시 적법하게 소유한 원본 디스크 덤프를 사용하세요. 최종 출력 ISO, xdelta와
릴리스 ZIP의 SHA-256은 릴리스 번들의 `SHA256SUMS.txt` 및
[`docs/releases/v0.4.0-alpha.1-verification.md`](docs/releases/v0.4.0-alpha.1-verification.md)에
기록합니다.

## 소스에서 ISO 생성

Python 3.10 이상, `requirements.txt`, 공식 빌드와 정확히 같은
`NanumSquareNeo-cBd.ttf`가 필요합니다. 폰트 파일은 원본 ISO와 같은 폴더에
두면 기본값으로 사용하며, 다른 위치라면 `--font`로 지정합니다.

```powershell
python -m pip install -r requirements.txt
python tools/patch_hyda_dialogue.py `
  "SO3_DC_Disc1_original.iso" `
  "SO3_DC_Disc1_Korean_Hyda_653_Nanum.iso" `
  --catalogue "translations/hyda_patch_manifest.json" `
  --translations "translations/hyda_ko.json" `
  --font "NanumSquareNeo-cBd.ttf" `
  --report "hyda_patch_report.json"
```

공식 빌드 글꼴 SHA-256:
`4749FA5691157CF56A59D297B45E88894A646846048018CD7A4117FFB2869767`

NanumSquare Neo는 NAVER와 Sandoll이 제공하며 SIL Open Font License 1.1을
따릅니다. 폰트 파일 자체는 저장소나 릴리스에 넣지 않습니다. 저작권 고지와
라이선스 전문은
[`LICENSES/NanumSquareNeo-COPYRIGHT.txt`](LICENSES/NanumSquareNeo-COPYRIGHT.txt)와
[`LICENSES/NanumSquareNeo-OFL-1.1.txt`](LICENSES/NanumSquareNeo-OFL-1.1.txt)를
참고하세요.

## 별도 정적 검증

검증기는 패처 모듈을 가져오지 않고 별도 PK1·메시지·글리프 검증 경로로 결과를
다시 분석합니다. 숨겨진 인덱스, SLZ 해제와 mclib 구조 파서는 공통 저수준
`so3_repack` 라이브러리를 사용합니다.

```powershell
python tools/verify_hyda_dialogue_iso.py `
  "SO3_DC_Disc1_original.iso" `
  "SO3_DC_Disc1_Korean_Hyda_653_Nanum.iso" `
  --catalogue "translations/hyda_patch_manifest.json" `
  --translations "translations/hyda_ko.json" `
  --font "NanumSquareNeo-cBd.ttf" `
  --report "hyda_verification.json"

python -m unittest discover -s tests -v
```

엄격 검증 항목은 다음과 같습니다.

- 숨겨진 6,144-entry 인덱스의 인코딩·디코딩 결과 불변
- 허용된 24개 아카이브 밖 ISO 변경 `0` bytes
- 653개 위치의 원문 SHA-256과 한국어 본문·화자 일치
- 각 대상 메시지의 이벤트·레이아웃 제어 서명 보존
- 비목표 메시지와 그 메시지가 참조하는 기존 로컬 글리프 불변
- 비목표 PK1 레코드와 이후 PK1 패키지·꼬리 불변
- 24×24 로컬 폰트 기하와 NanumSquare Neo 렌더링 일치

최종 빌드별 수치와 해시는
[`docs/releases/v0.4.0-alpha.1-verification.md`](docs/releases/v0.4.0-alpha.1-verification.md)에
기록합니다. 정적 검증 통과는 실제 에뮬레이터 출력 확인을 대신하지 않습니다.

## 현재 제한

- 게임 전체가 아니라 초반 하이다 구간의 확인된 653개 위치만 번역합니다.
- 번역 품질, 문맥, 화면 폭과 진행 흐름은 아직 사용자 런타임 검증 대상입니다.
- 지원 원본·폰트 해시, 653개 원문 세그먼트 해시, 매니페스트 구조 또는 번역
  커버리지가 다르면 패처나 검증기가 중단됩니다.
- 전역 숫자·영문·문장부호 코드의 의미는 기존 전역 atlas 분석 결과를 신뢰합니다.
  검증기는 전역 atlas가 ISO에서 바뀌지 않았음을 확인하지만 각 전역 글리프의
  의미를 다시 판독하지는 않습니다.
- ISO, 게임 실행 파일, 추출 게임 데이터, BIOS, PCSX2 세이브스테이트와 TTF
  폰트 파일은 저장소나 릴리스에 포함하지 않습니다.

## 법적 고지

이 프로젝트는 비공식 팬 연구 프로젝트이며 tri-Ace, Square Enix, Sony와 관계가
없습니다. 반드시 적법하게 소유한 원본 디스크 덤프를 사용하세요.
