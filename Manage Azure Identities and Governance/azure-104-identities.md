# AZ-104 섹션 1: Manage Azure Identities and Governance (15–20%)

> 최신 기준(2025년 Microsoft Learn, Microsoft Entra ID 명칭 반영)  
> 시험 대비 + 실무용 완전 정리판

---

## 목차
1. [개요](#개요)
2. [Azure AD (Microsoft Entra ID) 핵심 개념](#azure-ad-microsoft-entra-id-핵심-개념)
3. [Manage Azure AD Objects](#manage-azure-ad-objects)
4. [Manage Access Control (RBAC)](#manage-access-control-rbac)
5. [Manage Subscriptions and Governance](#manage-subscriptions-and-governance)
6. [Azure AD Plans (라이선스)](#azure-ad-plans-라이선스)
7. [하이브리드 ID & 인증](#하이브리드-id--인증)
8. [모니터링 및 감사](#모니터링-및-감사)
9. [시험 대비 체크리스트](#시험-대비-체크리스트)
10. [실무 베스트 프랙티스](#실무-베스트-프랙티스)

---

## 개요
- 섹션 비중: **15–20%**
- 주요 구성:
  - Azure AD 개체 관리 (Users, Groups, Devices, Guest, SSPR)
  - Access Control (RBAC)
  - Subscription / Governance (Policy, Tags, Locks, Resource Groups, Management Groups, Cost Management)

---

## Azure AD (Microsoft Entra ID) 핵심 개념

| 개념 | 설명 |
|------|------|
| **Tenant** | 조직의 신원 경계를 이루는 Entra ID 인스턴스 |
| **Subscription** | 과금 및 리소스 한계 단위 |
| **Resource Group** | 리소스 논리적 묶음, 배포/삭제 단위 |
| **Cloud Identity** | 클라우드에서만 관리되는 계정 |
| **Directory-synchronized Identity** | 온프레미스 AD에서 동기화된 계정 |
| **Guest User** | 외부 조직 또는 개인 초대 계정 (B2B 협업용) |

### AD DS vs Azure AD
| 항목 | AD DS | Azure AD (Entra ID) |
|------|--------|----------------------|
| 구조 | 트리형, OU/GPO 사용 | 평면 구조, OU 없음 |
| 프로토콜 | Kerberos, LDAP | SAML, OAuth, OpenID Connect |
| 관리 | 직접 서버 관리 | 완전 관리형 서비스 |
| 확장성 | 내부 네트워크 | 클라우드 기반, SaaS 통합 용이 |

---

## Manage Azure AD Objects

### 사용자 및 그룹
- **그룹 유형**
  - Security Group: 권한 관리용
  - Microsoft 365 Group: 협업용
- **멤버십**
  - Assigned (수동)
  - Dynamic (규칙 기반 자동)
- **대량 작업 (Bulk)**
  - CSV 업로드, PowerShell, CLI
  - 권한: Global Admin or User Admin

### Administrative Units (AU)
- 부서/지역 단위 관리 위임 컨테이너  
- 예: HR Admin이 HR 사용자만 관리 가능

### 디바이스
- **유형**
  - Azure AD Registered (개인기기)
  - Azure AD Joined (조직 기기)
  - Hybrid Azure AD Joined (온프레 + 클라우드)
- **추가 개념**
  - Intune 연계: MDM, 컴플라이언스 정책
  - Device Cleanup, Device Write-back 설정

### 게스트 계정 (B2B)
- 외부 사용자 초대 → 제한된 접근 부여  
- Access Review로 정기적 검증 및 만료 자동화

### SSPR (Self-Service Password Reset)
- 사용자가 직접 비밀번호 초기화 가능  
- 인증 방법: 전화, 이메일, 앱, 보안 질문  
- 라이선스 및 Usage Location 필요

---

## Manage Access Control (RBAC)

### 개념 구성
| 요소 | 설명 |
|------|------|
| Security Principal | 사용자/그룹/서비스 프린시펄 |
| Role | 권한 집합 (Built-in / Custom) |
| Scope | Management Group → Subscription → RG → Resource |
| Assignment | 주체 + 역할 + 범위 결합 |

### Built-in Roles
| 역할 | 설명 |
|------|------|
| Owner | 전체 권한 + 위임 가능 |
| Contributor | 모든 작업, 단 위임 불가 |
| Reader | 조회만 가능 |
| User Access Administrator | 접근 관리 전용 |

### Custom Role 예시
```json
{
  "Name": "VM Start Only",
  "Actions": ["Microsoft.Compute/virtualMachines/start/action"]
}
