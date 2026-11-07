# CW.dev

Cheolwoo Park의 개인 소개 사이트입니다. [Hugo](https://gohugo.io/)와 [LoveIt 테마](https://github.com/dillonzq/LoveIt)로 만들었고, GitHub Actions를 통해 GitHub Pages에 자동으로 배포합니다.

- 사이트: https://cheolu1025.github.io/MyWebpage_cw/
- 저장소: https://github.com/cheolu1025/MyWebpage_cw
- 배포 기록: https://github.com/cheolu1025/MyWebpage_cw/actions

## 폴더 구조

실제로 빌드되는 사이트는 `site/` 폴더에 있습니다. 저장소 루트의 나머지 파일(`layouts/`, `assets/`, `hugo.toml` 등)은 처음 내려받은 LoveIt 테마 ZIP의 원본이며 빌드에 사용하지 않습니다.

```text
.
├── .github/
│   ├── workflows/hugo-pages.yml   # main push 시 빌드·배포하는 워크플로
│   └── pages_build.py             # 워크플로가 호출하는 빌드 스크립트
├── .github-pages.json             # 빌드 설정(사용할 설정 파일, 테마 이름, 주소)
└── site/
    ├── hugo.toml                  # 사이트 설정(제목, 메뉴, 프로필, 언어 등)
    ├── pages.json                 # 배포용 추가 설정(baseURL, 테마 위치)
    ├── content/
    │   ├── about/index.ko.md      # About 페이지
    │   └── posts/<글 이름>/        # Activities 글(본문 index.ko.md와 첨부 파일)
    ├── assets/images/avatar.jpg   # 홈 프로필 이미지
    ├── layouts/                   # 테마 템플릿을 덮어쓴 파일
    └── themes/template/           # LoveIt 테마(폴더 복사 방식)
```

## 사용 도구와 버전

| 도구 | 버전 | 용도 |
| --- | --- | --- |
| Hugo (extended) | 0.167.0 | 사이트 빌드 |
| Dart Sass | 1.105.0 | 테마의 SCSS를 CSS로 변환 |
| Git | 최신 버전 | 버전 관리와 push |
| Python | 3.12 (워크플로) | 배포용 빌드 스크립트 실행 |
| Node.js | 24 (워크플로) | 테마 빌드 도구 설치 |

로컬에서 사이트를 확인할 때는 Hugo extended와 Dart Sass만 있으면 됩니다. Python과 Node.js는 GitHub Actions에서만 사용합니다.

## 설치

1. Hugo extended 0.167.0을 설치합니다. [Hugo 릴리스 페이지](https://github.com/gohugoio/hugo/releases/tag/v0.167.0)에서 운영체제에 맞는 `hugo_extended_0.167.0_*` 파일을 받습니다.
2. Dart Sass 1.105.0을 설치합니다. [Dart Sass 릴리스 페이지](https://github.com/sass/dart-sass/releases/tag/1.105.0)에서 파일을 받아 압축을 풀고, `sass` 실행 파일이 있는 폴더를 PATH에 추가합니다.
3. 설치를 확인합니다.

   ```bash
   hugo version
   sass --version
   ```

4. 저장소를 내려받습니다.

   ```bash
   git clone https://github.com/cheolu1025/MyWebpage_cw.git
   cd MyWebpage_cw
   ```

## 로컬에서 실행하기

모든 명령은 `site/` 폴더에서 실행합니다.

미리보기 서버를 켜고 브라우저에서 http://localhost:1313/MyWebpage_cw/ 를 엽니다. 파일을 저장하면 화면이 자동으로 새로고침됩니다.

```bash
cd site
hugo server --config hugo.toml,pages.json --theme template --themesDir themes
```

배포와 같은 방식으로 빌드 결과만 만들려면 다음 명령을 사용합니다. 결과는 저장소 루트의 `public/` 폴더에 생성되며, 이 폴더는 Git에 올라가지 않습니다.

```bash
cd site
hugo --gc --minify --config hugo.toml,pages.json --theme template --themesDir themes --destination ../public
```

## 내용 수정과 재배포

### 수정할 파일 위치

| 바꾸고 싶은 것 | 파일 |
| --- | --- |
| 사이트 제목, 메뉴, 홈 프로필 문구, 소셜 링크 | `site/hugo.toml` |
| About 페이지 | `site/content/about/index.ko.md` |
| Activities 글 | `site/content/posts/<글 이름>/index.ko.md` |
| 새 글 추가 | `site/content/posts/`에 새 폴더를 만들고 `index.ko.md` 작성 |
| 프로필 이미지 | `site/assets/images/avatar.jpg` |
| 화면 템플릿 수정 | `site/layouts/` (테마 원본 `site/themes/template/`은 수정하지 않음) |
| 색, 카드 모양 등 스타일 | `site/assets/css/_override.scss`(색 변수), `site/assets/css/_custom.scss`(추가 스타일) |
| 테마 전환 버튼 동작 | `site/assets/js/theme.js` (테마 원본을 복사해 라이트/다크 전환 부분만 수정) |

새 글의 앞부분(front matter)에는 `date`, `weight`(목록 순서, 작을수록 위), `categories`(Research, Studies, Projects, Experience 중 하나)를 넣습니다.

### 재배포 순서

1. 파일을 수정하고 로컬 미리보기로 확인합니다.
2. 수정한 파일만 골라 커밋하고 main 브랜치에 push합니다.

   ```bash
   git add <수정한 파일>
   git commit -m "변경 내용을 설명하는 메시지"
   git push
   ```

3. 저장소의 [Actions 탭](https://github.com/cheolu1025/MyWebpage_cw/actions)에서 방금 커밋 메시지와 같은 이름의 "Deploy Hugo to GitHub Pages" 실행이 초록색 체크로 끝나는지 확인합니다. 보통 1~3분 걸립니다.
4. https://cheolu1025.github.io/MyWebpage_cw/ 를 열어 바뀐 내용을 확인합니다. 이전 화면이 보이면 강력 새로고침(macOS `⌘ + Shift + R`, Windows `Ctrl + Shift + R`)을 하거나 시크릿 창으로 엽니다.

실행이 빨간색 X로 실패하면 해당 실행을 눌러 실패한 단계의 로그를 확인합니다. 빌드는 성공했는데 배포 단계만 시간 초과로 실패한 경우에는, 다음 push의 배포가 성공하면 그 사이 변경도 함께 반영됩니다.

## 출처와 라이선스

### 테마와 도구

| 자료 | 사용 위치 | 구분 | 원본 | 이용 조건 |
| --- | --- | --- | --- | --- |
| LoveIt 테마 | `site/themes/template/` 전체, `site/layouts/`의 덮어쓴 템플릿 | 외부 자료 | https://github.com/dillonzq/LoveIt | MIT License. 원본 고지는 `LICENSE`와 `site/themes/template/LICENSE`에 유지 |
| 사이트 아이콘(헤더 로고, favicon, 앱 아이콘) | `site/hugo.toml`의 헤더 로고, `site/static/`의 아이콘 파일 | 직접 제작 | 스캔 테두리 안의 눈과, 눈동자에서 퍼지는 이벤트 신호(점)를 표현해 SVG로 직접 디자인하고 크기별 PNG·ICO로 변환 | 직접 제작 자료 |
| Hugo | 사이트 빌드 | 외부 도구 | https://gohugo.io/ | Apache License 2.0 |
| 배포 준비 스크립트 | `prepare_github_pages.py`, `.github/pages_build.py`, `.github/workflows/hugo-pages.yml` | 외부 자료 (수업 제공) | 2026학년도 초급프로젝트 수업 제공 스크립트 | 수업 안내에 따름 |

### 이미지와 문서

| 자료 | 사용 위치 | 구분 | 원본·도구 |
| --- | --- | --- | --- |
| 홈 프로필 이미지 | `site/assets/images/avatar.jpg` | AI 생성 | ChatGPT 이미지 생성(모델명 미표시)으로 만든 뒤 원형에 맞게 자르고 크기를 줄임 |
| 공부 기록 발표 자료 PDF | `site/content/posts/study-*/*-slides.pdf` | 직접 제작 | 본인이 만든 발표 자료. 자료 안의 논문 그림은 각 자료의 참고문헌에 출처 표기 |
| Pick & Keep 제안 발표 PDF | `site/content/posts/project-pick-and-keep/` | 직접 제작 | 2인 팀 프로젝트 제안 발표 자료(팀원 이름 제외) |
| 바운딩 박스 비교 이미지 | `site/content/posts/research-bbox-yolo-qwen/` | 직접 제작 + 외부 자료 | 직접 실험해 만든 시각화. 원본 사진과 정답 박스는 [COCO 데이터셋](https://cocodataset.org/) |
| COALA 활동 계획 이미지 | `site/content/posts/experience-coala/` | 외부 자료 | COALA 동아리 신입 모집 홍보 자료 |
| 활동 글 대표 이미지(논문 구조 그림) | `site/content/posts/study-*/featured-image.jpg` | 외부 자료 | 각 논문의 구조 그림(CNN 구조, Swin Transformer, CLIP, VadCLIP, CPL-VAD). 본인 발표 자료에 인용한 그림을 잘라 사용, 글에 논문 출처 표기 |
| UCF-Crime-DVS 글 대표 이미지 | `site/content/posts/study-ucf-crime-dvs/featured-image.png` | 외부 자료 | [iniVation](https://inivation.com/)의 동적 비전 센서(DVS) 구동 원리 자료(일반 카메라와 DVS 출력 비교) |
| 이벤트 카메라 연구 글 대표 이미지 | `site/content/posts/dvs-anomaly-detection/featured-image.jpg` | 외부 자료 | [Prophesee](https://www.prophesee.ai/) YouTube 채널 영상의 일반 카메라와 이벤트 카메라 출력 비교 장면 |
| Pick & Keep 대표 이미지 | `site/content/posts/project-pick-and-keep/featured-image.jpg` | 직접 제작 | 제안 발표 자료의 시스템 구조 슬라이드 |
| COALA 글 대표 이미지 | `site/content/posts/experience-coala/featured-image.jpg` | 외부 자료 | COALA 동아리 2026년 2학기 부원 모집 홍보 자료 |
| 목록용 미리보기 이미지 | `site/content/posts/*/featured-image-preview.jpg` | 직접 가공 | 위 대표 이미지를 목록 카드 비율(가로형)에 맞게 여백을 넣어 다시 만든 이미지 |

### 글에서 인용한 논문

각 공부 기록 글에 논문 정보와 링크를 표기했습니다.

- UCF-Crime-DVS (Qian et al., AAAI 2025) — https://arxiv.org/abs/2503.12905
- Swin Transformer (Liu et al., ICCV 2021) — https://arxiv.org/abs/2103.14030
- ConvNeXt (Liu et al., CVPR 2022) — https://arxiv.org/abs/2201.03545
- CLIP (Radford et al., ICML 2021) — https://arxiv.org/abs/2103.00020
- VadCLIP (Wu et al., AAAI 2024) — https://arxiv.org/abs/2308.11681
- CPL-VAD (Lee et al., arXiv 2026) — https://arxiv.org/abs/2602.17077

### AI 도구 활용

사이트 설정, 글 문장 다듬기, 오류 분석과 동작 확인에 AI 도구(Claude Code 등)를 활용했습니다.
글의 내용은 본인이 실제로 수행한 연구, 공부, 프로젝트, 동아리 활동을 바탕으로 하며, AI가 제안한 문장과 설정은 직접 검토하고 수정해 반영했습니다.
