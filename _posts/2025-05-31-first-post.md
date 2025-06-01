---
title: 첫 블로깅 시작
date: 2025-05-31 17:40:00 +09:00
categories: [etc]
tags: [blog]
---

# 첫 블로깅 시작 🌟

Jekyll 테마를 사용해서 블로그를 만드는 과정을 정리해봤다. 나만의 블로그를 시작하려면 아래 단계를 따라 하면 된다.

---

## **Jekyll 테마 블로그 준비 사항** 📝

블로그를 시작하기 전에 아래 준비를 한다:

1. **GitHub 계정 및 Repository 생성**
  - [GitHub](https://github.com/)에 가입하고 새로운 저장소(repository)를 생성한다. 블로그를 공개하려면 저장소를 공개(Public)로 설정한다.

2. **Ruby 설치**  
   Jekyll 실행을 위해 Ruby가 필요하다. 운영체제에 맞게 Ruby를 설치한다.
  - Windows: [RubyInstaller](https://rubyinstaller.org/)로 설치.
  - macOS / Linux: 터미널에서 설치.
    ```bash
    # macOS (Homebrew로 설치)
    brew install ruby
    ```

---

## **Jekyll 테마 선택하기** 🎨

[Jekyll Themes](http://jekyllthemes.org/)에서 다양한 테마를 확인하고 마음에 드는 테마를 선택한다.   
원하는 테마가 있다면 아래 방법 중 하나를 선택한다:

1. **Fork 방법**
  - 마음에 드는 저장소를 선택한 뒤, GitHub 페이지 우측 상단의 **Fork**를 클릭해 자신의 GitHub 저장소로 복제한다.
  - 이후, 자신의 저장소를 로컬로 클론한다:
    ```bash
    git clone https://github.com/사용자명/저장소명.git
    ```

2. **다운로드 방법**
  - 테마 저장소로 이동해 **Code** 버튼을 클릭한 뒤, **Download ZIP**을 선택한다.
  - 다운로드한 ZIP 파일을 해당 디렉토리에 풀어서 사용하면 된다.

3. **Git Clone 방법**
  - 원본 저장소를 클론해 작업을 진행한다:
    ```bash
    git clone https://github.com/테마소유자명/테마저장소명.git
    ```

---

## **Jekyll 테마 적용하기** 🛠️

1. **의존성 설치**  
   테마 폴더로 이동한 뒤, 필요한 의존성을 설치한다.
   ```bash
   cd 테마폴더명
   bundle install
   ```

2. **로컬 서버 실행**  
   서버를 실행해 변경된 UI를 확인한다.
   ```bash
   bundle exec jekyll serve
   ```
   브라우저에서 `http://127.0.0.1:4000`로 접속해 확인한다.

---

## **GitHub에 커밋하고 Push하기** 🌐

1. **Git 초기화**  
   클론한 Jekyll 테마 디렉토리에서 Git을 초기화한다.
   ```bash
   git init
   ```

2. **GitHub 원격 저장소 연결**  
   기존 Jekyll 테마 디렉토리를 자신이 만든 GitHub 저장소에 연결한다.  
   (단, **Git clone**으로 작업한 경우 이미 `origin`으로 연결되어 있으니 필요 없다.)
   ```bash
   git remote add origin https://github.com/사용자명/저장소명.git
   ```

3. **변경 사항 커밋**  
   변경된 파일을 스테이징하고 커밋한다.
   ```bash
   git add .
   git commit -m "최초 Jekyll 블로그 설정"
   ```

4. **GitHub에 푸시**  
   커밋을 GitHub 원격 저장소의 `main` 브랜치로 푸시한다.
   ```bash
   git branch -M main
   git push -u origin main
   ```

---

## **블로그 게시 및 배포** 🚀

1. **GitHub Pages 설정**  
   GitHub 저장소에서 **Settings** > **Pages** 항목으로 이동해 Source를 `main` 브랜치로 설정한다.

2. **사이트 확인**  
   설정이 완료되면 GitHub Pages가 블로그를 자동으로 배포한다.  
   블로그 URL은 `https://사용자명.github.io` 형식으로 제공된다.
