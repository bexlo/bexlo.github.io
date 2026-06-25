# BEXLO 홈페이지 보안 점검 조치 결과 보고서

- 대상 사이트: https://www.bexlo.co.kr
- 점검일시: 2026-06-25
- 인프라 구성: GitHub Pages + Cloudflare(Proxy)
- 보고 목적: 보안 점검 지적 5개 항목에 대한 조치 및 재검증 결과 제출

## 1. 요약

이번 조치로 보안 헤더 및 HTTPS 정책이 Cloudflare 응답 헤더 기준으로 정상 반영되었으며, 리디렉션 루프 문제도 해소되었습니다.

- 최종 서비스 URL은 https://www.bexlo.co.kr 로 정상 수렴
- CSP에서 unsafe-inline / unsafe-eval 제거 확인
- HSTS 적용 확인
- X-Content-Type-Options 적용 확인
- X-Frame-Options 적용 확인

## 2. 항목별 조치 및 판정

### 항목 1) 콘텐츠 보안 정책에 unsafe-* 지시 포함

- 지적 내용: CSP에 unsafe-inline, unsafe-eval 포함
- 조치 내용:
  - Cloudflare 응답 헤더의 Content-Security-Policy를 강화 정책으로 교체
  - script-src-attr 'none', style-src-attr 'none' 포함
- 검증 증적 (2026-06-25):
  - curl -I https://www.bexlo.co.kr/
  - 확인 헤더:
    - Content-Security-Policy: default-src 'self'; script-src 'self'; script-src-attr 'none'; style-src 'self' https://fonts.googleapis.com; style-src-elem 'self' https://fonts.googleapis.com; style-src-attr 'none'; img-src 'self' data: https:; font-src 'self' data: https://fonts.gstatic.com; connect-src 'self'; frame-src https://www.google.com https://maps.google.com; frame-ancestors 'self'; object-src 'none'; base-uri 'self'; form-action 'self'; upgrade-insecure-requests
- 판정: 조치 완료

### 항목 2) HSTS 미구현

- 지적 내용: no_hsts
- 조치 내용:
  - Cloudflare에서 Strict-Transport-Security 헤더 적용
  - 값: max-age=31536000; includeSubDomains; preload
- 검증 증적:
  - curl -I https://www.bexlo.co.kr/
  - 확인 헤더: Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
- 판정: 조치 완료

### 항목 3) 안전하지 않은 HTTPS 리디렉션 패턴

- 지적 내용: http://bexlo.co.kr/ -> https://www.bexlo.co.kr/ (서브도메인 변경 동반)
- 조치 내용:
  - 리디렉션 규칙 정리로 루프 제거
  - 현재 체인:
    - http://bexlo.co.kr/ -> https://bexlo.co.kr/ -> https://www.bexlo.co.kr/
  - www 경로:
    - http://www.bexlo.co.kr/ -> https://www.bexlo.co.kr/
- 검증 증적:
  - curl -I -L --max-redirs 10 http://bexlo.co.kr/
  - curl -I -L --max-redirs 10 http://www.bexlo.co.kr/
  - 최종 응답: HTTP/1.1 200 OK (https://www.bexlo.co.kr/)
- 판정: 조치 완료

### 항목 4) X-Content-Type-Options 미구현

- 지적 내용: x_content_type_options_missing
- 조치 내용:
  - Cloudflare에서 X-Content-Type-Options: nosniff 헤더 주입
- 검증 증적:
  - curl -I https://www.bexlo.co.kr/
  - 확인 헤더: X-Content-Type-Options: nosniff
- 판정: 조치 완료

### 항목 5) 악성 콘텐츠 임베드 방어 미흡

- 지적 내용: no_csp_header_x_frame_options_missing
- 조치 내용:
  - Cloudflare에서 X-Frame-Options: SAMEORIGIN 적용
  - CSP에 frame-ancestors 'self' 적용
- 검증 증적:
  - curl -I https://www.bexlo.co.kr/
  - 확인 헤더:
    - X-Frame-Options: SAMEORIGIN
    - Content-Security-Policy ... frame-ancestors 'self' ...
- 판정: 조치 완료

## 3. 리디렉션 및 서비스 가용성 확인 결과

- https://www.bexlo.co.kr/ : 200 OK
- https://bexlo.co.kr/ : 301 -> https://www.bexlo.co.kr/
- http://www.bexlo.co.kr/ : 301 -> https://www.bexlo.co.kr/ -> 200 OK
- http://bexlo.co.kr/ : 301 -> https://bexlo.co.kr/ -> 301 -> https://www.bexlo.co.kr/ -> 200 OK

## 4. 결론

보안 점검에서 지적된 5개 항목은 현재 운영 응답 헤더 및 리디렉션 체인 기준으로 모두 조치 완료 상태입니다.

다만 스캐너 캐시/지역 POP 캐시 영향으로 단기 재점검 시 과거 결과가 일부 재현될 수 있어, 재점검은 5~10분 간격으로 2회 이상 수행을 권장합니다.

## 5. 재검증 명령어(제출 부록)

- curl -I https://www.bexlo.co.kr/
- curl -I https://bexlo.co.kr/
- curl -I -L --max-redirs 10 http://www.bexlo.co.kr/
- curl -I -L --max-redirs 10 http://bexlo.co.kr/
