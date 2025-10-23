# 1. 섹션 개요(시험 비중 15–20%)

- 이 섹션은 크게 **(1) Azure AD 개체 관리**, **(2) 액세스 제어(RBAC)**, **(3) 구독/거버넌스** 세 축으로 구성됨.
- 공식 가이드의 하위 항목(Users/Groups/Devices/Guest/SSPR, RBAC 역할/범위/해석, Policy/Locks/Tags/Resource Group/Subscriptions/Costs/Management Groups)을 **모두** 다룸.

---

# 2. Azure AD(현: Microsoft Entra ID) 핵심 개념

## 2.1 용어/구성요소 정리

- **Tenant(디렉터리)**: 조직의 신원 경계를 이루는 Entra ID 인스턴스.
- **Subscription(구독)**: 과금/청구 및 리소스 사용 한계의 단위(1개 테넌트에 다수 구독 연결 가능).
- **Resource Group**: 리소스의 논리적 묶음(배포·관리·삭제 단위).
- **Identity 유형**
    - **Cloud Identity**: Entra에만 존재하는 사용자/앱 계정.
    - **Directory-synchronized Identity**: 온프레미스 AD에서 동기화된 계정(Azure AD Connect).
    - **Guest(External) User**: 외부 조직/개인 초대 계정(B2B 협업).

> 시험 포인트
> 
> - *Tenant/Subscription/Resource Group/Resource* **계층과 상속** 개념을 함께 기억(이후 RBAC/Policy/Lock이 이 계층을 따라 적용/상속됨).
> - 게스트 계정은 협업에 유용하지만, **정기 Access Review**와 최소권한이 중요.

---

# 3. Manage Azure AD objects (개체 관리)

> 공식 가이드 항목:
> 
> 
> **Create users & groups / Admin Units / Properties / Device settings / Bulk updates / Guest / SSPR** 등을 포함하고 있습니다.
> 

## 3.1 사용자·그룹 생성/관리

- **그룹 유형**
    - 보안 그룹(Security): 권한·액세스 제어에 사용(시험 빈출).
    - Microsoft 365 그룹: 협업(Teams/메일/SharePoint).
- **멤버십 방식**
    - 할당형(Assigned): 수동 추가/제거.
    - **동적(Dynamic)**: 속성 규칙으로 자동 포함(예: `department == "Sales"`).
- **대량 작업(Bulk)**
    - CSV 업로드, PowerShell/CLI 스크립트로 대량 생성/갱신(**시험 키워드: Bulk create/update users**).

## 3.2 Administrative Units(AU)

- 조직 단위(부서/지역 등)별로 **관리 권한을 위임**하는 컨테이너. Helpdesk/부서관리자에게 특정 사용자·그룹만 관리하도록 범위를 제한.

## 3.3 사용자·그룹 속성

- 사용자 프로필(UPN, Display Name, Usage Location 등)과 그룹 설명/분류 편집. 스크립트(Set-AzureADUser / Set-AzureADGroup)로 일괄 조정.

## 3.4 디바이스 설정/디바이스 ID

- **등록/조인 유형**
    - **Azure AD Registered**(BYOD/개인 기기 등록),
    - **Azure AD Joined**(조직 소유 기기, Entra 직접 조인),
    - **Hybrid Azure AD Joined**(온프레 AD 조인 + Entra 등록).
- 포털에서 디바이스 등록 허용, 조인 요건, MDM(예: Intune) 필요 여부 등의 **정책 수준** 제어가 가능.

> 실무·배경 확장
> 
> - Intune과 결합 시 **MDM Authority/컴플라이언스 정책/조건부 액세스**로 장치 상태 기반 접근 통제가 가능.
> - 장치 정리(Device cleanup rules), **디바이스 쓰기 되돌리기(Device write-back)**(하이브리드) 고려.

## 3.5 게스트 계정(B2B)

- 외부 사용자 초대(이메일 기반), **제한된 접근** 부여. 정기 **Access Review**로 만료/미사용 계정 정비.

## 3.6 SSPR(Self-Service Password Reset)

- 사용자가 스스로 암호 재설정 가능(대상 범위: 모든 사용자/일부/꺼짐). **인증 방법**(전화/SMS/앱/보조 이메일/보안 질문 등) 최소 1개 이상 구성. **배포 고려사항/커스터마이즈 가이드** 존재.

---

# 4. Manage access control (RBAC)

> 공식 가이드 항목: Custom role / Role assignment at scopes / Interpret access.
> 

## 4.1 핵심 개념

- **보안 주체(Security Principal)**: 사용자/그룹/서비스 프린시펄/관리 ID.
- **역할(Role)**: 권한 집합(내장/사용자정의).
- **범위(Scope)**: Management Group → Subscription → Resource Group → Resource(하위 상속).
- **할당(Role Assignment)**: 주체 + 역할 + 범위를 결합해 실제 권한 부여.

## 4.2 내장 역할(Built-in) 요약

- **Owner**: 모든 작업 + 액세스 위임.
- **Contributor**: 모든 리소스 생성/변경(단, 권한 위임 불가).
- **Reader**: 보기 전용.
- **User Access Administrator**: 액세스(역할)만 관리.

> 주의: 위 4개는 Azure 리소스 RBAC 역할이며, Entra(AD) 관리 역할과 다름(예: Global Admin, User Admin 등). 시험에서 혼동 주의!
> 

## 4.3 사용자 정의 역할(Custom Role)

- JSON으로 허용/거부 가능한 **액션**을 명시해 최소권한을 구현(예: VM Start/Read만 허용).

## 4.4 역할 해석(Interpret assignments)

- 포털(IAM)에서 **누가/어떤 범위에/어떤 역할**이 있는지 확인. **Deny assignment**가 있으면 허용보다 우선.

> 실무·배경 확장
> 
> - **PIM(Privileged Identity Management)**: 관리자/상위 역할을 **JIT(Just-in-Time)**로 요청·승인·만료시켜 상시 권한을 최소화(보안 필수).
> - **Managed Identity**를 사용하면 앱/자동화가 자격증명 없이 Azure 서비스에 안전하게 인증.

---

# 5. Manage subscriptions and governance(구독·거버넌스)

> 공식 가이드 항목: Policy / Locks / Tags / Resource Groups / Subscriptions / Costs / Management Groups.
> 

## 5.1 Azure Policy

- **조직 규정 준수**를 리소스 수준에서 **강제/감사/자동 보정**하는 정책 엔진.
- 구성 요소: **정의(Definition)**, **할당(Assignment)**, **매개변수(Parameter)**, **이니셔티브(Initiative: 정책 묶음)**.
- **효과(Effect)** 종류(시험 포인트)
    - **Audit**(감사), **Deny**(거부), **Append**(필드 추가), **DeployIfNotExists**(존재 안 하면 배포), **Modify**(속성 수정)
- 대표 시나리오
    - 리소스 배포 **허용 위치 제한**(데이터 주권), **태그 강제**, **백업/진단 설정 필수화**, **VM SKU 제한** 등.
- 절차 개요: 정의 작성(JSON) → 적정 **Scope**(관리 그룹/구독/RG)에 **할당** → **컴플라이언스 대시보드**로 상태·시정(Remediation) 추적.

## 5.2 Resource Locks(리소스 잠금)

- 종류: **Delete**(삭제 방지), **ReadOnly**(읽기 전용). RG에 잠그면 소속 리소스가 **상속**. **중요 프로덕션** 보호에 필수.

## 5.3 Tags

- Key-Value 메타데이터(예: `Environment=Prod`, `CostCenter=Fin`). **비용 배분/검색/정책 연계**에 유용. **거버넌스 정책**으로 태그 강제 가능.

## 5.4 Resource Groups

- 리소스의 **수명주기/권한/정책/잠금/태그**를 묶어 관리. 배포/삭제 단위.(주의: 삭제는 되돌릴 수 없음)

## 5.5 Subscriptions(구독)

- **청구·할당량 경계**. 조직은 다수 구독으로 환경/팀/워크로드 분리. **RBAC/Policy/Locks/Tags**는 구독과 그 하위에 상속 적용.

## 5.6 비용 관리(Cost Management + Billing)

- **Cost analysis/예산·알림/Budget**, **Advisor 비용 최적화 권장**, **예약/스팟/절감플랜**, **하이브리드 혜택** 활용.(과금은 리소스·지역·전송 방향에 따라 상이)

## 5.7 Management Groups

- **여러 구독을 상위 스코프**로 묶어 **정책·RBAC 일괄 적용/상속**. **Root management group** 하위에 계층 구성.

---

# 6. Azure AD Plan(라이선스) 정리(시험+실무)

- **Free / Microsoft 365 Apps / Premium P1 / Premium P2**
    - **P1**: 동적그룹, 그룹 self-service, 고급 액세스 관리, **조건부 액세스** 등
    - **P2**: **Identity Protection(위험 기반 정책)**, **PIM(특권 JIT)**, **Access Review/Identity Governance** 등
- **라이선스 할당 실무 포인트**
    - 사용자 **Usage Location** 지정 후 라이선스 할당(그룹 기반 라이선스 권장—자동 상속).
    - 그룹 상속으로 받은 라이선스는 사용자에서 직접 제거 불가(그룹에서 제거).

---

# 7. 하이브리드 ID & 인증(실무·배경 확장)

> ※ 업로드된 공식 가이드는 “Azure AD Connect; PIM”을 NOT 표기했지만(당시 버전 한정), 최신 학습·실무에선 필수 배경이라 간단 정리합니다.
> 
- **Azure AD Connect**: 온프레미스 AD ↔ Entra 동기화(객체/암호 해시 등).
- **인증 옵션**
    - **Password Hash Sync(PHS)**: 암호 해시 동기화(가장 단순/권장 시나리오).
    - **Pass-through Authentication(PTA)**: 온프레 AD로 직접 인증 위임.
    - **Federation(AD FS)**: 고급 연동/커스텀 요구 시.
    - **Seamless SSO**: 도메인 내부 자동 SSO.
- **보안·거버넌스 보강**
    - **Conditional Access**: 사용자·디바이스·위험·위치·앱·세션 조건으로 접근 제어.
    - **Identity Protection**: 위험 로그인/사용자 리스크 기반 정책.
    - **PIM**: 상시 관리자 권한을 제거하고 **요청-승인-만료** 기반으로 부여.
    - **Access Review**: 게스트/그룹/앱 접근의 **주기적 검토·만료** 자동화.

---

# 8. 모니터링/감사(실무·배경 확장)

- **Sign-in logs / Audit logs**: 로그인/구성 변경 추적.
- **Azure Monitor + Log Analytics**: 테넌트 및 구독 전반의 진단/경고/자동화.
- **활용 팁**
    - Workbooks로 **보안/운영 대시보드** 구성.
    - SSPR/조건부 액세스/리소스 정책의 **적용 상태와 실패 이벤트**를 쿼리로 점검.

---

# 9. 시험 대비 체크리스트(요점·함정)

- **Entra 역할**(Global/User/Groups/Helpdesk 등) **vs 리소스 RBAC 역할**(Owner/Contributor/Reader…) **구분**.
- **Scope 상속**: 관리 그룹→구독→RG→리소스로 **정책/역할/락**이 전파(예외: 제외(Exclusions)와 Deny).
- **Policy Effect** 5종(**Audit/Deny/Append/DeployIfNotExists/Modify**)과 **이니셔티브** 개념.
- **SSPR** 구성/인증수단/적용대상 옵션(모두/일부/꺼짐)과 **라이선스/Usage Location** 요건.
- **게스트(B2B)** 초대/권한 최소화/Access Review.
- **대량 작업(Bulk)**: CSV/스크립트 권한 요건(전형적으로 Global Admin or User Admin).
- **Tags** 제한(최대 50쌍, 특수문자 제한, 스토리지 계정 태그 이름 길이 제한 주의), **비용 분석**과 연계.
- **Resource Lock**은 **RG에 걸면 하위 리소스 상속**(삭제 방지 실수 문제).
- **Management Group**으로 구독 다수를 상위에서 일괄 통제.

---

# 10. 실무 베스트 프랙티스(한눈에)

- **최소권한 원칙**: 그룹 기반 RBAC + Custom Role로 권한 축소.
- **JIT 관리**: PIM으로 상시 권한 제거, **승인·기록** 필수.
- **정책 우선 관리**: Azure Policy로 표준·보안·태그·백업 **강제/자동 시정**.
- **게스트 거버넌스**: Access Review/만료 규칙, **조건부 액세스**로 리스크 완화.
- **MDM/디바이스**: Intune 기반 컴플라이언스 + 조건부 액세스 **연합**.
- **모니터링**: 로그인/감사/활동/진단 로그를 **워크북/경고**로 상시 관측.
- **비용 최적화**: 예산·알림, 예약/절감플랜, **Advisor 권장** 주기 반영.

---

## 근거(업로드한 공식 가이드 내 해당 파트)

- 섹션 구성 및 범위(Identities & Governance, 하위 항목 전반)
- Manage Azure AD objects(Users/Groups/AU/Devices/Bulk/Guest/SSPR) 항목 열거
- RBAC(역할/범위/해석, 사용자 정의 역할) 항목 열거
- 거버넌스(Policy/Locks/Tags/RG/Subscriptions/Costs/Management Groups) 항목 열거
- (참고) 당시 버전에서 *NOT: Azure AD Connect; PIM* 표기