Internet Engineering Task Force (IETF)  D.




Hardt, Ed.Request for Comments: 6749   MicrosoftObsoletes: 5849   October 2012

Category: Standards TrackISSN: 2070-1721  



The OAuth 2.0 Authorization FrameworkAbstract The OAuth 2.0 authorization framework enables a third-party application to obtain limited access to an HTTP service, either on behalf of a resource owner by orchestrating an approval interaction between the resource owner and the HTTP service, or by allowing the third-party application to obtain access on its own behalf. This specification replaces and obsoletes the OAuth 1.0 protocol described in RFC 5849.Status of This Memo This is an Internet Standards Track document. This document is a product of the Internet Engineering Task Force (IETF). It represents the consensus of the IETF community. It has received public review and has been approved for publication by the Internet Engineering Steering Group (IESG). Further information on Internet Standards is available in Section 2 of RFC 5741. Information about the current status of this document, any errata, and how to provide feedback on it may be obtained at http://www.rfc-editor.org/info/rfc6749.Copyright Notice Copyright (c) 2012 IETF Trust and the persons identified as the document authors. All rights reserved. This document is subject to BCP 78 and the IETF Trust's Legal Provisions Relating to IETF Documents (http://trustee.ietf.org/license-info) in effect on the date of publication of this document. Please review these documents carefully, as they describe your rights and restrictions with respect to this document. Code Components extracted from this document must include Simplified BSD License text as described in Section 4.e of the Trust Legal Provisions and are provided without warranty as described in the Simplified BSD License.


OAuth 2.0 인증 프레임 워크 개요 OAuth 2.0 인증 프레임 워크를 사용하면 리소스 소유자를 대신하여 리소스 소유자와 HTTP 서비스 간의 승인 상호 작용을 조정하거나 다음을 허용하여 타사 애플리케이션이 HTTP 서비스에 대한 제한된 액세스 권한을 얻을 수 있습니다. 타사 응용 프로그램을 사용하여 자체적으로 액세스 권한을 얻습니다. 이 사양은 RFC 5849에 설명 된 OAuth 1.0 프로토콜을 대체하고 폐기합니다.이 메모의 상태 이것은 인터넷 표준 추적 문서입니다. 이 문서는 IETF (Internet Engineering Task Force)의 제품입니다. IETF 커뮤니티의 합의를 나타냅니다. 공개 검토를 받았으며 IESG (Internet Engineering Steering Group)의 게시 승인을 받았습니다. 인터넷 표준에 대한 추가 정보는 RFC 5741의 섹션 2에서 확인할 수 있습니다.이 문서의 현재 상태, 정오표 및 이에 대한 피드백 제공 방법에 대한 정보는 http://www.rfc-editor.org/info에서 얻을 수 있습니다. /rfc6749.Copyright Notice Copyright (c) 2012 IETF Trust 및 문서 작성자로 확인 된 사람. 판권 소유. 이 문서는 BCP 78 및 IETF 문서와 관련된 IETF Trust의 법적 조항 (http://trustee.ietf.org/license-info)의 적용을받습니다. 이 문서에 대한 귀하의 권리 및 제한 사항을 설명하므로이 문서를주의 깊게 검토하십시오. 이 문서에서 추출 된 코드 구성 요소는 신뢰 법률 조항의 섹션 4.e에 설명 된대로 단순화 된 BSD 라이센스 텍스트를 포함해야하며 단순화 된 BSD 라이센스에 설명 된대로 보증없이 제공됩니다.





 1. Introduction In the traditional client-server authentication model, the client requests an access-restricted resource (protected resource) on the server by authenticating with the server using the resource owner's credentials. In order to provide third-party applications access to restricted resources, the resource owner shares its credentials with the third party. This creates several problems and limitations: o Third-party applications are required to store the resource owner's credentials for future use, typically a password in clear-text. o Servers are required to support password authentication, despite the security weaknesses inherent in passwords. o Third-party applications gain overly broad access to the resource owner's protected resources, leaving resource owners without any ability to restrict duration or access to a limited subset of resources. o Resource owners cannot revoke access to an individual third party without revoking access to all third parties, and must do so by changing the third party's password.

1. 소개 기존의 클라이언트-서버 인증 모델에서 클라이언트는 리소스 소유자의 자격 증명을 사용하여 서버에 인증함으로써 서버에서 액세스가 제한된 리소스 (보호 된 리소스)를 요청합니다. 제한된 리소스에 대한 타사 애플리케이션 액세스를 제공하기 위해 리소스 소유자는 해당 자격 증명을 타사와 공유합니다. 이로 인해 몇 가지 문제와 제한 사항이 발생합니다. o 향후 사용을 위해 리소스 소유자의 자격 증명을 저장하려면 타사 응용 프로그램이 필요합니다. 일반적으로 일반 텍스트의 암호입니다. o 암호에 내재 된 보안 취약점에도 불구하고 서버는 암호 인증을 지원해야합니다. o 타사 응용 프로그램은 리소스 소유자의 보호 된 리소스에 대해 지나치게 광범위하게 액세스 할 수 있으므로 리소스 소유자는 기간을 제한하거나 제한된 리소스 하위 집합에 대한 액세스 권한을 갖지 못합니다. o 리소스 소유자는 모든 제 3 자에 대한 액세스를 취소하지 않고 개별 제 3 자에 대한 액세스를 취소 할 수 없으며 제 3 자의 비밀번호를 변경하여이를 취소해야합니다.







 o Compromise of any third-party application results in compromise of the end-user's password and all of the data protected by that password. OAuth addresses these issues by introducing an authorization layer and separating the role of the client from that of the resource owner. In OAuth, the client requests access to resources controlled by the resource owner and hosted by the resource server, and is issued a different set of credentials than those of the resource owner. Instead of using the resource owner's credentials to access protected resources, the client obtains an access token -- a string denoting a specific scope, lifetime, and other access attributes. Access tokens are issued to third-party clients by an authorization server with the approval of the resource owner. The client uses the access token to access the protected resources hosted by the resource server. For example, an end-user (resource owner) can grant a printing service (client) access to her protected photos stored at a photo- sharing service (resource server), without sharing her username and password with the printing service. Instead, she authenticates directly with a server trusted by the photo-sharing service (authorization server), which issues the printing service delegation- specific credentials (access token). This specification is designed for use with HTTP ([RFC2616]). The use of OAuth over any protocol other than HTTP is out of scope. The OAuth 1.0 protocol ([RFC5849]), published as an informational document, was the result of a small ad hoc community effort. This Standards Track specification builds on the OAuth 1.0 deployment experience, as well as additional use cases and extensibility requirements gathered from the wider IETF community. The OAuth 2.0 protocol is not backward compatible with OAuth 1.0. The two versions may co-exist on the network, and implementations may choose to support both. However, it is the intention of this specification that new implementations support OAuth 2.0 as specified in this document and that OAuth 1.0 is used only to support existing deployments. The OAuth 2.0 protocol shares very few implementation details with the OAuth 1.0 protocol. Implementers familiar with OAuth 1.0 should approach this document without any assumptions as to its structure and details.



o 타사 응용 프로그램이 손상되면 최종 사용자의 암호와 해당 암호로 보호되는 모든 데이터가 손상됩니다. OAuth는 권한 부여 계층을 도입하고 리소스 소유자의 역할과 클라이언트의 역할을 분리하여 이러한 문제를 해결합니다. OAuth에서 클라이언트는 리소스 소유자가 제어하고 리소스 서버에서 호스팅하는 리소스에 대한 액세스를 요청하고 리소스 소유자와 다른 자격 증명 집합을 발급받습니다. 리소스 소유자의 자격 증명을 사용하여 보호 된 리소스에 액세스하는 대신 클라이언트는 특정 범위, 수명 및 기타 액세스 속성을 나타내는 문자열 인 액세스 토큰을 얻습니다. 액세스 토큰은 리소스 소유자의 승인을 받아 권한 부여 서버에서 타사 클라이언트에 발급됩니다. 클라이언트는 액세스 토큰을 사용하여 리소스 서버에서 호스팅하는 보호 된 리소스에 액세스합니다. 예를 들어, 최종 사용자 (자원 소유자)는 사용자 이름과 암호를 인쇄 서비스와 공유하지 않고 사진 공유 서비스 (자원 서버)에 저장된 보호 된 사진에 대한 인쇄 서비스 (클라이언트) 액세스 권한을 부여 할 수 있습니다. 대신 그녀는 인쇄 서비스 위임 특정 자격 증명 (액세스 토큰)을 발급하는 사진 공유 서비스 (권한 부여 서버)에서 신뢰하는 서버로 직접 인증합니다. 이 사양은 HTTP ([RFC2616])와 함께 사용하도록 설계되었습니다. HTTP 이외의 프로토콜을 통한 OAuth 사용은 범위를 벗어납니다. 정보 문서로 게시 된 OAuth 1.0 프로토콜 ([RFC5849])은 소규모 임시 커뮤니티 노력의 결과였습니다. 이 표준 트랙 사양은 OAuth 1.0 배포 경험뿐만 아니라 광범위한 IETF 커뮤니티에서 수집 한 추가 사용 사례 및 확장 성 요구 사항을 기반으로합니다. OAuth 2.0 프로토콜은 OAuth 1.0과 역 호환되지 않습니다. 두 버전이 네트워크에 공존 할 수 있으며 구현시 둘 다 지원하도록 선택할 수 있습니다. 그러나 새로운 구현이이 문서에 지정된대로 OAuth 2.0을 지원하고 OAuth 1.0이 기존 배포를 지원하는 데만 사용되는 것이이 사양의 의도입니다. OAuth 2.0 프로토콜은 OAuth 1.0 프로토콜과 구현 세부 사항을 거의 공유하지 않습니다. OAuth 1.0에 익숙한 구현자는 구조와 세부 사항에 대한 가정없이이 문서에 접근해야합니다.






1.1. Roles
OAuth defines four roles:

resource owner  
An entity capable of granting access to a protected resource. When the resource owner is a person, it is referred to as an end-user.

resource server  
The server hosting the protected resources, capable of accepting and responding to protected resource requests using access tokens.

client  
An application making protected resource requests on behalf of the resource owner and with its authorization. The term "client" does not imply any particular implementation characteristics (e.g., whether the application executes on a server, a desktop, or other devices).

authorization server  
The server issuing access tokens to the client after successfully authenticating the resource owner and obtaining authorization. The interaction between the authorization server and resource server is beyond the scope of this specification. The authorization server may be the same server as the resource server or a separate entity. A single authorization server may issue access tokens accepted by multiple resource servers.



1.1. 역할
OAuth는 네 가지 역할을 정의합니다.

리소스 소유자  
보호 된 리소스에 대한 액세스 권한을 부여 할 수있는 엔티티입니다. 리소스 소유자가 개인 인 경우이를 최종 사용자라고합니다.

리소스 서버  
액세스 토큰을 사용하여 보호 된 리소스 요청을 수락하고 응답 할 수있는 보호 된 리소스를 호스팅하는 서버입니다.

클라이언트  
리소스 소유자를 대신하여 권한을 부여하여 보호 된 리소스를 요청하는 응용 프로그램입니다. "클라이언트"라는 용어는 특정 구현 특성 (예 : 응용 프로그램이 서버, 데스크톱 또는 기타 장치에서 실행되는지 여부)을 의미하지 않습니다.

권한 서버  
리소스 소유자를 성공적으로 인증하고 권한을 얻은 후 클라이언트에 액세스 토큰을 발급하는 서버입니다. 권한 부여 서버와 리소스 서버 간의 상호 작용은이 사양의 범위를 벗어납니다. 권한 서버는 자원 서버와 동일한 서버이거나 별도의 엔티티 일 수 있습니다. 단일 인증 서버는 여러 리소스 서버에서 허용하는 액세스 토큰을 발급 할 수 있습니다.





1.2. Protocol Flow

     +--------+                               +---------------+
     |        |--(A)- Authorization Request ->|   Resource    |
     |        |                               |     Owner     |
     |        |<-(B)-- Authorization Grant ---|               |
     |        |                               +---------------+
     |        |
     |        |                               +---------------+
     |        |--(C)-- Authorization Grant -->| Authorization |
     | Client |                               |     Server    |
     |        |<-(D)----- Access Token -------|               |
     |        |                               +---------------+
     |        |
     |        |                               +---------------+
     |        |--(E)----- Access Token ------>|    Resource   |
     |        |                               |     Server    |
     |        |<-(F)--- Protected Resource ---|               |
     +--------+                               +---------------+

                     Figure 1: Abstract Protocol Flow


The abstract OAuth 2.0 flow illustrated in Figure 1 describes the interaction between the four roles and includes the following steps: (A) The client requests authorization from the resource owner. The authorization request can be made directly to the resource owner (as shown), or preferably indirectly via the authorization server as an intermediary. (B) The client receives an authorization grant, which is a credential representing the resource owner's authorization, expressed using one of four grant types defined in this specification or using an extension grant type. The authorization grant type depends on the method used by the client to request authorization and the types supported by the authorization server. (C) The client requests an access token by authenticating with the authorization server and presenting the authorization grant. (D) The authorization server authenticates the client and validates the authorization grant, and if valid, issues an access token.


그림 1에 나와있는 추상 OAuth 2.0 흐름은 네 가지 역할 간의 상호 작용을 설명하며 다음 단계를 포함합니다. (A) 클라이언트가 리소스 소유자에게 권한 부여를 요청합니다. 권한 부여 요청은 리소스 소유자 (표시된대로)에게 직접 만들어 지거나, 중개자로서 권한 부여 서버를 통해 간접적으로 수행 할 수 있습니다. (B) 클라이언트는이 사양에 정의 된 네 가지 부여 유형 중 하나를 사용하거나 확장 부여 유형을 사용하여 표현 된 자원 소유자의 권한을 나타내는 자격 증명 인 인증 부여를받습니다. 권한 부여 유형은 클라이언트가 권한을 요청하는 데 사용하는 방법과 권한 서버가 지원하는 유형에 따라 다릅니다. (C) 클라이언트는 권한 부여 서버로 인증하고 권한 부여를 제시하여 액세스 토큰을 요청합니다. (D) 권한 부여 서버는 클라이언트를 인증하고 권한 부여의 유효성을 확인하고 유효한 경우 액세스 토큰을 발급합니다.



 (E) The client requests the protected resource from the resource server and authenticates by presenting the access token. (F) The resource server validates the access token, and if valid, serves the request. The preferred method for the client to obtain an authorization grant from the resource owner (depicted in steps (A) and (B)) is to use the authorization server as an intermediary, which is illustrated in Figure 3 in Section 4.1.

 (E) 클라이언트는 리소스 서버에서 보호 된 리소스를 요청하고 액세스 토큰을 제시하여 인증합니다. (F) 리소스 서버는 액세스 토큰의 유효성을 검사하고 유효한 경우 요청을 처리합니다. 클라이언트가 자원 소유자 (단계 (A) 및 (B)에 설명 됨)로부터 권한 부여를 얻기 위해 선호되는 방법은 권한 부여 서버를 중개자로 사용하는 것입니다. 이는 섹션 4.1의 그림 3에 설명되어 있습니다.






 1.3. Authorization Grant
 An authorization grant is a credential representing the resource owner's authorization (to access its protected resources) used by the client to obtain an access token. This specification defines four grant types -- authorization code, implicit, resource owner password credentials, and client credentials -- as well as an extensibility mechanism for defining additional types.



 1.3. Authorization Grant
 권한 부여는 클라이언트가 액세스 토큰을 얻기 위해 사용하는 리소스 소유자의 권한 (보호 된 리소스에 액세스하기위한)을 나타내는 자격 증명입니다. 이 사양은 인증 코드, 암시 적, 리소스 소유자 암호 자격 증명 및 클라이언트 자격 증명의 네 가지 부여 유형과 추가 유형을 정의하기위한 확장 성 메커니즘을 정의합니다.




 1.3.1. Authorization Code

 The authorization code is obtained by using an authorization server as an intermediary between the client and resource owner. Instead of requesting authorization directly from the resource owner, the client directs the resource owner to an authorization server (via its user-agent as defined in [RFC2616]), which in turn directs the resource owner back to the client with the authorization code. Before directing the resource owner back to the client with the authorization code, the authorization server authenticates the resource owner and obtains authorization. Because the resource owner only authenticates with the authorization server, the resource owner's credentials are never shared with the client. The authorization code provides a few important security benefits, such as the ability to authenticate the client, as well as the transmission of the access token directly to the client without passing it through the resource owner's user-agent and potentially exposing it to others, including the resource owner.


 1.3.1. Authorization Code
 권한 코드는 클라이언트와 자원 소유자 사이의 중개자로서 권한 서버를 사용하여 얻습니다. 리소스 소유자에게 직접 권한을 요청하는 대신 클라이언트는 리소스 소유자를 권한 부여 서버 ([RFC2616]에 정의 된대로 사용자 에이전트를 통해)로 보내고, 다시 권한 코드를 사용하여 리소스 소유자를 클라이언트로 다시 보냅니다. 권한 코드를 사용하여 자원 소유자를 클라이언트로 다시 보내기 전에 권한 서버는 자원 소유자를 인증하고 권한을 얻습니다. 리소스 소유자는 권한 부여 서버로만 인증하기 때문에 리소스 소유자의 자격 증명은 클라이언트와 공유되지 않습니다. 인증 코드는 클라이언트를 인증하는 기능과 같은 몇 가지 중요한 보안 이점을 제공 할뿐만 아니라 액세스 토큰을 리소스 소유자의 사용자 에이전트를 통해 전달하지 않고 클라이언트로 직접 전송하여 잠재적으로 자원 소유자를 포함하여 다른 사용자에게 노출 할 수 있습니다.






 1.3.2. Implicit  
The implicit grant is a simplified authorization code flow optimized for clients implemented in a browser using a scripting language such as JavaScript. In the implicit flow, instead of issuing the client an authorization code, the client is issued an access token directly (as the result of the resource owner authorization). The grant type is implicit, as no intermediate credentials (such as an authorization code) are issued (and later used to obtain an access token). When issuing an access token during the implicit grant flow, the authorization server does not authenticate the client. In some cases, the client identity can be verified via the redirection URI used to deliver the access token to the client. The access token may be exposed to the resource owner or other applications with access to the resource owner's user-agent. Implicit grants improve the responsiveness and efficiency of some clients (such as a client implemented as an in-browser application), since it reduces the number of round trips required to obtain an access token. However, this convenience should be weighed against the security implications of using implicit grants, such as those described in Sections 10.3 and 10.16, especially when the authorization code grant type is available.



 1.3.2. Implicit  
암시 적 권한 부여는 JavaScript와 같은 스크립팅 언어를 사용하여 브라우저에서 구현 된 클라이언트에 최적화 된 단순화 된 인증 코드 흐름입니다. 암시 적 흐름에서 클라이언트에게 권한 부여 코드를 발급하는 대신 클라이언트에 직접 액세스 토큰이 발급됩니다 (자원 소유자 권한의 결과로). 권한 부여 코드와 같은 중간 자격 증명이 발급되지 않고 나중에 액세스 토큰을 얻는 데 사용되기 때문에 부여 유형은 암시 적입니다. 암시 적 허용 흐름 중에 액세스 토큰을 발행 할 때 권한 부여 서버는 클라이언트를 인증하지 않습니다. 경우에 따라 클라이언트에 액세스 토큰을 전달하는 데 사용되는 리디렉션 URI를 통해 클라이언트 ID를 확인할 수 있습니다. 액세스 토큰은 리소스 소유자 또는 리소스 소유자의 사용자 에이전트에 대한 액세스 권한이있는 다른 애플리케이션에 노출 될 수 있습니다. 암시 적 권한 부여는 액세스 토큰을 얻는 데 필요한 왕복 횟수를 줄이므로 일부 클라이언트 (예 : 브라우저 내 애플리케이션으로 구현 된 클라이언트)의 응답 성과 효율성을 향상시킵니다. 그러나 이러한 편의는 특히 권한 부여 코드 부여 유형을 사용할 수있는 경우 섹션 10.3 및 10.16에 설명 된 것과 같은 암시 적 부여 사용의 보안 의미와 비교되어야합니다.






1.3.3. Resource Owner Password Credentials  
The resource owner password credentials (i.e., username and password) can be used directly as an authorization grant to obtain an access token. The credentials should only be used when there is a high degree of trust between the resource owner and the client (e.g., the client is part of the device operating system or a highly privileged application), and when other authorization grant types are not available (such as an authorization code). Even though this grant type requires direct client access to the resource owner credentials, the resource owner credentials are used for a single request and are exchanged for an access token. This grant type can eliminate the need for the client to store the resource owner credentials for future use, by exchanging the credentials with a long-lived access token or refresh token.


1.3.3. 리소스 소유자 암호 자격 증명  
리소스 소유자 암호 자격 증명 (즉, 사용자 이름 및 암호)은 액세스 토큰을 얻기위한 권한 부여로 직접 사용할 수 있습니다. 자격 증명은 리소스 소유자와 클라이언트간에 높은 수준의 신뢰가있을 때 (예 : 클라이언트가 장치 운영 체제 또는 높은 권한을 가진 응용 프로그램의 일부 임) 및 다른 권한 부여 유형을 사용할 수없는 경우에만 사용해야합니다 ( 승인 코드 등). 이 권한 부여 유형에는 리소스 소유자 자격 증명에 대한 직접적인 클라이언트 액세스가 필요하지만 리소스 소유자 자격 증명은 단일 요청에 사용되며 액세스 토큰으로 교환됩니다. 이 권한 부여 유형은 자격 증명을 수명이 긴 액세스 토큰 또는 새로 고침 토큰으로 교환하여 클라이언트가 나중에 사용할 수 있도록 리소스 소유자 자격 증명을 저장할 필요를 제거 할 수 있습니다.





1.3.4. Client Credentials
The client credentials (or other forms of client authentication) can be used as an authorization grant when the authorization scope is limited to the protected resources under the control of the client, or to protected resources previously arranged with the authorization server. Client credentials are used as an authorization grant typically when the client is acting on its own behalf (the client is also the resource owner) or is requesting access to protected resources based on an authorization previously arranged with the authorization server.

1.3.4. 클라이언트 자격 증명
클라이언트 자격 증명 (또는 다른 형태의 클라이언트 인증)은 권한 부여 범위가 클라이언트의 제어하에있는 보호 된 자원으로 제한되거나 이전에 권한 부여 서버와 함께 배열 된 보호 된 자원으로 제한 될 때 권한 부여로 사용될 수 있습니다. 클라이언트 자격 증명은 일반적으로 클라이언트가 자신을 대신하여 작동하거나 (클라이언트가 리소스 소유자이기도 함) 이전에 권한 부여 서버에 배열 된 권한을 기반으로 보호 된 리소스에 대한 액세스를 요청할 때 권한 부여로 사용됩니다.




1.4. Access Token
Access tokens are credentials used to access protected resources. An access token is a string representing an authorization issued to the client. The string is usually opaque to the client. Tokens represent specific scopes and durations of access, granted by the resource owner, and enforced by the resource server and authorization server. The token may denote an identifier used to retrieve the authorization information or may self-contain the authorization information in a verifiable manner (i.e., a token string consisting of some data and a signature). Additional authentication credentials, which are beyond the scope of this specification, may be required in order for the client to use a token. The access token provides an abstraction layer, replacing different authorization constructs (e.g., username and password) with a single token understood by the resource server. This abstraction enables issuing access tokens more restrictive than the authorization grant used to obtain them, as well as removing the resource server's need to understand a wide range of authentication methods. Access tokens can have different formats, structures, and methods of utilization (e.g., cryptographic properties) based on the resource server security requirements. Access token attributes and the methods used to access protected resources are beyond the scope of this specification and are defined by companion specifications such as [RFC6750].


1.4. 액세스 토큰
액세스 토큰은 보호 된 리소스에 액세스하는 데 사용되는 자격 증명입니다. 액세스 토큰은 클라이언트에 발급 된 권한을 나타내는 문자열입니다. 문자열은 일반적으로 클라이언트에게 불투명합니다. 토큰은 자원 소유자가 부여하고 자원 서버 및 권한 부여 서버에 의해 시행되는 특정 범위 및 액세스 기간을 나타냅니다. 토큰은 인증 정보를 검색하는 데 사용되는 식별자를 나타내거나 검증 가능한 방식으로 인증 정보를 자체 포함 할 수 있습니다 (즉, 일부 데이터 및 서명으로 구성된 토큰 문자열). 클라이언트가 토큰을 사용하려면이 사양의 범위를 벗어나는 추가 인증 자격 증명이 필요할 수 있습니다. 액세스 토큰은 추상화 계층을 제공하여 서로 다른 인증 구성 (예 : 사용자 이름 및 비밀번호)을 리소스 서버가 이해하는 단일 토큰으로 대체합니다. 이 추상화를 통해 액세스 토큰을 얻는 데 사용 된 권한 부여보다 더 제한적인 액세스 토큰을 발급 할 수있을뿐만 아니라 광범위한 인증 방법을 이해해야하는 리소스 서버의 필요성을 제거 할 수 있습니다. 액세스 토큰은 리소스 서버 보안 요구 사항에 따라 다양한 형식, 구조 및 활용 방법 (예 : 암호화 속성)을 가질 수 있습니다. 보호 된 리소스에 액세스하는 데 사용되는 액세스 토큰 속성 및 방법은이 사양의 범위를 벗어나며 [RFC6750]과 같은 동반 사양에 의해 정의됩니다.





1.5. Refresh Token
Refresh tokens are credentials used to obtain access tokens. Refresh tokens are issued to the client by the authorization server and are used to obtain a new access token when the current access token becomes invalid or expires, or to obtain additional access tokens with identical or narrower scope (access tokens may have a shorter lifetime and fewer permissions than authorized by the resource owner). Issuing a refresh token is optional at the discretion of the authorization server. If the authorization server issues a refresh token, it is included when issuing an access token (i.e., step (D) in Figure 1). A refresh token is a string representing the authorization granted to the client by the resource owner. The string is usually opaque to the client. The token denotes an identifier used to retrieve the authorization information. Unlike access tokens, refresh tokens are intended for use only with authorization servers and are never sent to resource servers.

1.5. 새로 고침 토큰
새로 고침 토큰은 액세스 토큰을 얻는 데 사용되는 자격 증명입니다. 새로 고침 토큰은 권한 부여 서버에 의해 클라이언트에 발급되며 현재 액세스 토큰이 유효하지 않거나 만료 될 때 새 액세스 토큰을 얻거나 범위가 동일하거나 더 좁은 추가 액세스 토큰을 얻는 데 사용됩니다 (액세스 토큰의 수명이 더 짧을 수 있으며 리소스 소유자가 승인 한 것보다 적은 권한). 새로 고침 토큰 발급은 권한 부여 서버의 재량에 따라 선택 사항입니다. 인증 서버가 새로 고침 토큰을 발행하면 액세스 토큰을 발행 할 때 포함됩니다 (예 : 그림 1의 단계 (D)). 새로 고침 토큰은 리소스 소유자가 클라이언트에 부여한 권한을 나타내는 문자열입니다. 문자열은 일반적으로 클라이언트에게 불투명합니다. 토큰은 인증 정보를 검색하는 데 사용되는 식별자를 나타냅니다. 액세스 토큰과 달리 새로 고침 토큰은 권한 부여 서버에서만 사용하기위한 것이며 리소스 서버로 전송되지 않습니다.


  +--------+                                           +---------------+
  |        |--(A)------- Authorization Grant --------->|               |
  |        |                                           |               |
  |        |<-(B)----------- Access Token -------------|               |
  |        |               & Refresh Token             |               |
  |        |                                           |               |
  |        |                            +----------+   |               |
  |        |--(C)---- Access Token ---->|          |   |               |
  |        |                            |          |   |               |
  |        |<-(D)- Protected Resource --| Resource |   | Authorization |
  | Client |                            |  Server  |   |     Server    |
  |        |--(E)---- Access Token ---->|          |   |               |
  |        |                            |          |   |               |
  |        |<-(F)- Invalid Token Error -|          |   |               |
  |        |                            +----------+   |               |
  |        |                                           |               |
  |        |--(G)----------- Refresh Token ----------->|               |
  |        |                                           |               |
  |        |<-(H)----------- Access Token -------------|               |
  +--------+           & Optional Refresh Token        +---------------+

               Figure 2: Refreshing an Expired Access Token


 The flow illustrated in Figure 2 includes the following steps: (A) The client requests an access token by authenticating with the authorization server and presenting an authorization grant. (B) The authorization server authenticates the client and validates the authorization grant, and if valid, issues an access token and a refresh token. (C) The client makes a protected resource request to the resource server by presenting the access token. (D) The resource server validates the access token, and if valid, serves the request. (E) Steps (C) and (D) repeat until the access token expires. If the client knows the access token expired, it skips to step (G); otherwise, it makes another protected resource request. (F) Since the access token is invalid, the resource server returns an invalid token error.
 (G) The client requests a new access token by authenticating with the authorization server and presenting the refresh token. The client authentication requirements are based on the client type and on the authorization server policies. (H) The authorization server authenticates the client and validates the refresh token, and if valid, issues a new access token (and, optionally, a new refresh token). Steps (C), (D), (E), and (F) are outside the scope of this specification, as described in Section 7.


 그림 2에 설명 된 흐름에는 다음 단계가 포함됩니다. (A) 클라이언트는 권한 부여 서버로 인증하고 권한 부여를 제공하여 액세스 토큰을 요청합니다. (B) 권한 부여 서버는 클라이언트를 인증하고 권한 부여의 유효성을 검사하고 유효한 경우 액세스 토큰과 새로 고침 토큰을 발급합니다. (C) 클라이언트는 액세스 토큰을 제시하여 리소스 서버에 보호 리소스 요청을합니다. (D) 리소스 서버는 액세스 토큰의 유효성을 검사하고 유효한 경우 요청을 처리합니다. (E) 액세스 토큰이 만료 될 때까지 (C) 및 (D) 단계를 반복합니다. 클라이언트가 액세스 토큰이 만료되었음을 알고 있으면 단계 (G)로 건너 뜁니다. 그렇지 않으면 다른 보호 된 리소스 요청을합니다. (F) 액세스 토큰이 유효하지 않기 때문에 리소스 서버가 유효하지 않은 토큰 오류를 반환합니다.
 (G) 클라이언트는 권한 서버로 인증하고 새로 고침 토큰을 제시하여 새 액세스 토큰을 요청합니다. 클라이언트 인증 요구 사항은 클라이언트 유형 및 권한 부여 서버 정책을 기반으로합니다. (H) 권한 부여 서버는 클라이언트를 인증하고 새로 고침 토큰의 유효성을 검사하고 유효한 경우 새 액세스 토큰 (및 선택적으로 새 새로 고침 토큰)을 발급합니다. 단계 (C), (D), (E) 및 (F)는 섹션 7에 설명 된대로이 사양의 범위를 벗어납니다.


 1.6. TLS Version
 Whenever Transport Layer Security (TLS) is used by this specification, the appropriate version (or versions) of TLS will vary over time, based on the widespread deployment and known security vulnerabilities. At the time of this writing, TLS version 1.2 [RFC5246] is the most recent version, but has a very limited deployment base and might not be readily available for implementation. TLS version 1.0 [RFC2246] is the most widely deployed version and will provide the broadest interoperability. Implementations MAY also support additional transport-layer security mechanisms that meet their security requirements.

 1.6. TLS 버전
  이 사양에서 TLS (전송 계층 보안)를 사용할 때마다 TLS의 적절한 버전 (또는 버전)은 광범위한 배포 및 알려진 보안 취약성에 따라 시간이 지남에 따라 달라집니다. 이 글을 쓰는 시점에서 TLS 버전 1.2 [RFC5246]가 가장 최신 버전이지만 배포 기반이 매우 제한적이며 구현에 쉽게 사용할 수 없습니다. TLS 버전 1.0 [RFC2246]은 가장 널리 배포 된 버전이며 가장 광범위한 상호 운용성을 제공합니다. 구현은 보안 요구 사항을 충족하는 추가 전송 계층 보안 메커니즘을 지원할 수도 있습니다 (MAY).



 1.7. HTTP Redirections
 This specification makes extensive use of HTTP redirections, in which the client or the authorization server directs the resource owner's user-agent to another destination. While the examples in this specification show the use of the HTTP 302 status code, any other method available via the user-agent to accomplish this redirection is allowed and is considered to be an implementation detail.


1.7. HTTP 리디렉션
  이 사양은 클라이언트 또는 권한 부여 서버가 리소스 소유자의 사용자 에이전트를 다른 대상으로 보내는 HTTP 리디렉션을 광범위하게 사용합니다. 이 사양의 예제는 HTTP 302 상태 코드의 사용을 보여 주지만,이 리디렉션을 수행하기 위해 사용자 에이전트를 통해 사용할 수있는 다른 모든 방법이 허용되며 구현 세부 사항으로 간주됩니다.


 1.8. Interoperability
 OAuth 2.0 provides a rich authorization framework with well-defined security properties. However, as a rich and highly extensible framework with many optional components, on its own, this specification is likely to produce a wide range of non-interoperable implementations. In addition, this specification leaves a few required components partially or fully undefined (e.g., client registration, authorization server capabilities, endpoint discovery). Without these components, clients must be manually and specifically configured against a specific authorization server and resource server in order to interoperate. This framework was designed with the clear expectation that future work will define prescriptive profiles and extensions necessary to achieve full web-scale interoperability.

 1.8. 상호 운용성
  OAuth 2.0은 잘 정의 된 보안 속성과 함께 풍부한 권한 부여 프레임 워크를 제공합니다. 그러나 많은 선택적 구성 요소가있는 풍부하고 확장 성이 뛰어난 프레임 워크로서이 사양은 상호 운용이 불가능한 광범위한 구현을 생성 할 가능성이 높습니다. 또한이 사양은 몇 가지 필수 구성 요소를 부분적으로 또는 완전히 정의되지 않은 상태로 둡니다 (예 : 클라이언트 등록, 권한 부여 서버 기능, 엔드 포인트 검색). 이러한 구성 요소가없는 경우 클라이언트는 상호 운용하기 위해 특정 권한 서버 및 자원 서버에 대해 수동으로 그리고 구체적으로 구성되어야합니다. 이 프레임 워크는 향후 작업이 완전한 웹 규모 상호 운용성을 달성하는 데 필요한 규범 적 프로필 및 확장을 정의 할 것이라는 분명한 기대로 설계되었습니다.






 1.9. Notational Conventions
 The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this specification are to be interpreted as described in [RFC2119]. This specification uses the Augmented Backus-Naur Form (ABNF) notation of [RFC5234]. Additionally, the rule URI-reference is included from "Uniform Resource Identifier (URI): Generic Syntax" [RFC3986]. Certain security-related terms are to be understood in the sense defined in [RFC4949]. These terms include, but are not limited to, "attack", "authentication", "authorization", "certificate", "confidentiality", "credential", "encryption", "identity", "sign", "signature", "trust", "validate", and "verify". Unless otherwise noted, all the protocol parameter names and values are case sensitive.

 1.9. 표기법
  이 사양에서 "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY"및 "OPTIONAL"의 키워드는 다음과 같습니다. [RFC2119]에 설명 된대로 해석됩니다. 이 사양은 [RFC5234]의 ABNF (Augmented Backus-Naur Form) 표기법을 사용합니다. 또한 규칙 URI 참조는 "URI (Uniform Resource Identifier) : 일반 구문"[RFC3986]에서 포함됩니다. 특정 보안 관련 용어는 [RFC4949]에 정의 된 의미로 이해되어야합니다. 이러한 용어는 "공격", "인증", "승인", "인증서", "기밀성", "자격 증명", "암호화", "신원", "서명", "서명"을 포함하지만 이에 국한되지 않습니다. "신뢰", "검증"및 "확인". 달리 명시되지 않는 한 모든 프로토콜 매개 변수 이름과 값은 대소 문자를 구분합니다.




 2. Client Registration

 Before initiating the protocol, the client registers with the authorization server. The means through which the client registers with the authorization server are beyond the scope of this specification but typically involve end-user interaction with an HTML registration form. Client registration does not require a direct interaction between the client and the authorization server. When supported by the authorization server, registration can rely on other means for establishing trust and obtaining the required client properties (e.g., redirection URI, client type). For example, registration can be accomplished using a self-issued or third-party-issued assertion, or by the authorization server performing client discovery using a trusted channel.

 When registering a client, the client developer SHALL: o specify the client type as described in Section 2.1, o provide its client redirection URIs as described in Section 3.1.2, and o include any other information required by the authorization server (e.g., application name, website, description, logo image, the acceptance of legal terms).



 2. 클라이언트 등록

 프로토콜을 시작하기 전에 클라이언트는 인증 서버에 등록합니다. 클라이언트가 인증 서버에 등록하는 수단은이 사양의 범위를 벗어나지 만 일반적으로 최종 사용자와 HTML 등록 양식과의 상호 작용을 포함합니다. 클라이언트 등록에는 클라이언트와 권한 부여 서버 간의 직접적인 상호 작용이 필요하지 않습니다. 인증 서버에서 지원하는 경우 등록은 신뢰를 설정하고 필요한 클라이언트 속성 (예 : 리디렉션 URI, 클라이언트 유형)을 얻기위한 다른 수단에 의존 할 수 있습니다. 예를 들어, 등록은 자체 발행 또는 제 3 자 발행 어설 션을 사용하거나 신뢰할 수있는 채널을 사용하여 클라이언트 발견을 수행하는 권한 서버에 의해 수행 될 수 있습니다.

 클라이언트를 등록 할 때 클라이언트 개발자는 다음을 수행해야합니다. o 2.1 절에 설명 된대로 클라이언트 유형을 지정하고, o 3.1.2 절에 설명 된대로 클라이언트 리디렉션 URI를 제공하고, o 인증 서버에 필요한 기타 정보 (예 : 응용 프로그램 이름, 웹 사이트, 설명, 로고 이미지, 법률 용어 수락).




 2.1. Client Types
 OAuth defines two client types, based on their ability to authenticate securely with the authorization server (i.e., ability to maintain the confidentiality of their client credentials):

 confidential
 Clients capable of maintaining the confidentiality of their credentials (e.g., client implemented on a secure server with restricted access to the client credentials), or capable of secure client authentication using other means.

 public
 Clients incapable of maintaining the confidentiality of their credentials (e.g., clients executing on the device used by the resource owner, such as an installed native application or a web browser-based application), and incapable of secure client authentication via any other means.

 The client type designation is based on the authorization server's definition of secure authentication and its acceptable exposure levels of client credentials. The authorization server SHOULD NOT make assumptions about the client type. A client may be implemented as a distributed set of components, each with a different client type and security context (e.g., a distributed client with both a confidential server-based component and a public browser-based component). If the authorization server does not provide support for such clients or does not provide guidance with regard to their registration, the client SHOULD register each component as a separate client.


2.1. 클라이언트 유형
 OAuth는 인증 서버로 안전하게 인증 할 수있는 능력 (즉, 클라이언트 자격 증명의 기밀성을 유지하는 능력)에 따라 두 가지 클라이언트 유형을 정의합니다.

 기밀
 자격 증명의 기밀성을 유지할 수있는 클라이언트 (예 : 클라이언트 자격 증명에 대한 액세스가 제한된 보안 서버에 구현 된 클라이언트) 또는 다른 수단을 사용하여 클라이언트 인증을 보호 할 수 있습니다.

 공공의
 자격 증명의 기밀성을 유지할 수없는 클라이언트 (예 : 설치된 기본 응용 프로그램 또는 웹 브라우저 기반 응용 프로그램과 같이 리소스 소유자가 사용하는 장치에서 실행되는 클라이언트) 및 다른 수단을 통한 보안 클라이언트 인증이 불가능합니다.

 클라이언트 유형 지정은 권한 부여 서버의 보안 인증 정의와 클라이언트 자격 증명의 허용 가능한 노출 수준을 기반으로합니다. 권한 서버는 클라이언트 유형에 대해 가정하지 않아야합니다. 클라이언트는 서로 다른 클라이언트 유형 및 보안 컨텍스트 (예 : 기밀 서버 기반 구성 요소와 공용 브라우저 기반 구성 요소를 모두 포함하는 분산 클라이언트)를 가진 분산 된 구성 요소 집합으로 구현 될 수 있습니다. 권한 부여 서버가 이러한 클라이언트에 대한 지원을 제공하지 않거나 등록에 대한 지침을 제공하지 않는 경우 클라이언트는 각 구성 요소를 별도의 클라이언트로 등록해야합니다.






 This specification has been designed around the following client profiles:

 web application
 A web application is a confidential client running on a web server. Resource owners access the client via an HTML user interface rendered in a user-agent on the device used by the resource owner. The client credentials as well as any access token issued to the client are stored on the web server and are not exposed to or accessible by the resource owner.

 user-agent-based application
 A user-agent-based application is a public client in which the client code is downloaded from a web server and executes within a user-agent (e.g., web browser) on the device used by the resource owner. Protocol data and credentials are easily accessible (and often visible) to the resource owner. Since such applications reside within the user-agent, they can make seamless use of the user-agent capabilities when requesting authorization.

 native application
 A native application is a public client installed and executed on the device used by the resource owner. Protocol data and credentials are accessible to the resource owner. It is assumed that any client authentication credentials included in the application can be extracted. On the other hand, dynamically issued credentials such as access tokens or refresh tokens can receive an acceptable level of protection. At a minimum, these credentials are protected from hostile servers with which the application may interact. On some platforms, these credentials might be protected from other applications residing on the same device.


 2.2. Client Identifier
 The authorization server issues the registered client a client identifier -- a unique string representing the registration information provided by the client. The client identifier is not a secret; it is exposed to the resource owner and MUST NOT be used alone for client authentication. The client identifier is unique to the authorization server. The client identifier string size is left undefined by this specification. The client should avoid making assumptions about the identifier size. The authorization server SHOULD document the size of any identifier it issues.



이 사양은 다음 클라이언트 프로필을 중심으로 설계되었습니다.

 웹 애플리케이션
 웹 애플리케이션은 웹 서버에서 실행되는 기밀 클라이언트입니다. 리소스 소유자는 리소스 소유자가 사용하는 장치의 사용자 에이전트에서 렌더링 된 HTML 사용자 인터페이스를 통해 클라이언트에 액세스합니다. 클라이언트 자격 증명 및 클라이언트에 발급 된 모든 액세스 토큰은 웹 서버에 저장되며 리소스 소유자에게 노출되거나 액세스 할 수 없습니다.

 사용자 에이전트 기반 애플리케이션
 사용자 에이전트 기반 애플리케이션은 클라이언트 코드가 웹 서버에서 다운로드되고 리소스 소유자가 사용하는 장치의 사용자 에이전트 (예 : 웹 브라우저) 내에서 실행되는 공용 클라이언트입니다. 프로토콜 데이터 및 자격 증명은 리소스 소유자가 쉽게 액세스 할 수 있으며 종종 볼 수 있습니다. 이러한 응용 프로그램은 사용자 에이전트 내에 있으므로 권한 부여를 요청할 때 사용자 에이전트 기능을 원활하게 사용할 수 있습니다.


 native application
 네이티브 애플리케이션은 리소스 소유자가 사용하는 장치에 설치 및 실행되는 공용 클라이언트입니다. 리소스 소유자는 프로토콜 데이터 및 자격 증명에 액세스 할 수 있습니다. 응용 프로그램에 포함 된 모든 클라이언트 인증 자격 증명을 추출 할 수 있다고 가정합니다. 반면에 액세스 토큰 또는 새로 고침 토큰과 같이 동적으로 발급 된 자격 증명은 허용 가능한 수준의 보호를받을 수 있습니다. 최소한 이러한 자격 증명은 애플리케이션이 상호 작용할 수있는 적대적인 서버로부터 보호됩니다. 일부 플랫폼에서는 이러한 자격 증명이 동일한 장치에있는 다른 응용 프로그램으로부터 보호 될 수 있습니다.


2.2. Client Identifier
인증 서버는 등록 된 클라이언트에게 클라이언트 식별자 (클라이언트가 제공 한 등록 정보를 나타내는 고유 한 문자열)를 발급합니다. 클라이언트 식별자는 비밀이 아닙니다. 리소스 소유자에게 노출되며 클라이언트 인증을 위해 단독으로 사용해서는 안됩니다. 클라이언트 식별자는 권한 부여 서버에 고유합니다. 클라이언트 식별자 문자열 크기는이 사양에 정의되지 않은 상태로 남아 있습니다. 클라이언트는 식별자 크기에 대한 가정을 피해야합니다. 인증 서버는 발행하는 식별자의 크기를 문서화해야합니다.





2.3. Client Authentication
If the client type is confidential, the client and authorization server establish a client authentication method suitable for the security requirements of the authorization server. The authorization server MAY accept any form of client authentication meeting its security requirements. Confidential clients are typically issued (or establish) a set of client credentials used for authenticating with the authorization server (e.g., password, public/private key pair). The authorization server MAY establish a client authentication method with public clients. However, the authorization server MUST NOT rely on public client authentication for the purpose of identifying the client. The client MUST NOT use more than one authentication method in each request.



2.3.1. Client Password
Clients in possession of a client password MAY use the HTTP Basic authentication scheme as defined in [RFC2617] to authenticate with the authorization server. The client identifier is encoded using the "application/x-www-form-urlencoded" encoding algorithm per Appendix B, and the encoded value is used as the username; the client password is encoded using the same algorithm and used as the password. The authorization server MUST support the HTTP Basic authentication scheme for authenticating clients that were issued a client password.


2.3. 클라이언트 인증
클라이언트 유형이 기밀 인 경우 클라이언트 및 권한 서버는 권한 서버의 보안 요구 사항에 적합한 클라이언트 인증 방법을 설정합니다. 권한 부여 서버는 보안 요구 사항을 충족하는 모든 형태의 클라이언트 인증을 수락 할 수 있습니다. 기밀 클라이언트는 일반적으로 권한 부여 서버 (예 : 암호, 공개 / 개인 키 쌍)로 인증하는 데 사용되는 클라이언트 자격 증명 집합을 발급 (또는 설정)합니다. 권한 서버는 공용 클라이언트와 클라이언트 인증 방법을 설정할 수 있습니다. 그러나 권한 부여 서버는 클라이언트 식별을 위해 공용 클라이언트 인증에 의존해서는 안됩니다. 클라이언트는 각 요청에서 하나 이상의 인증 방법을 사용해서는 안됩니다.



2.3.1. 클라이언트 비밀번호
클라이언트 암호를 소유 한 클라이언트는 [RFC2617]에 정의 된 HTTP 기본 인증 체계를 사용하여 권한 부여 서버로 인증 할 수 있습니다. 클라이언트 식별자는 부록 B에 따라 "application / x-www-form-urlencoded"인코딩 알고리즘을 사용하여 인코딩되며 인코딩 된 값은 사용자 이름으로 사용됩니다. 클라이언트 암호는 동일한 알고리즘을 사용하여 인코딩되고 암호로 사용됩니다. 권한 부여 서버는 클라이언트 암호를 발급 한 클라이언트를 인증하기 위해 HTTP 기본 인증 체계를 지원해야합니다.





For example (with extra line breaks for display purposes only):

Authorization: Basic czZCaGRSa3F0Mzo3RmpmcDBaQnIxS3REUmJuZlZkbUl3


Alternatively, the authorization server MAY support including the client credentials in the request-body using the following parameters:

client_id
REQUIRED. The client identifier issued to the client during the registration process described by Section 2.2.

client_secret
REQUIRED. The client secret. The client MAY omit the parameter if the client secret is an empty string.

Including the client credentials in the request-body using the two parameters is NOT RECOMMENDED and SHOULD be limited to clients unable to directly utilize the HTTP Basic authentication scheme (or other password-based HTTP authentication schemes). The parameters can only be transmitted in the request-body and MUST NOT be included in the request URI.


 For example, a request to refresh an access token (Section 6) using
 the body parameters (with extra line breaks for display purposes
 only):

  POST /token HTTP/1.1
  Host: server.example.com
  Content-Type: application/x-www-form-urlencoded

  grant_type=refresh_token&refresh_token=tGzv3JOkF0XG5Qx2TlKWIA
  &client_id=s6BhdRkqt3&client_secret=7Fjfp0ZBr1KtDRbnfVdmIw


The authorization server MUST require the use of TLS as described in Section 1.6 when sending requests using password authentication. Since this client authentication method involves a password, the authorization server MUST protect any endpoint utilizing it against brute force attacks.



예를 들어 (표시 목적으로 만 추가 줄 바꿈 포함) :

Authorization: Basic czZCaGRSa3F0Mzo3RmpmcDBaQnIxS3REUmJuZlZkbUl3


또는 권한 부여 서버는 다음 매개 변수를 사용하여 요청 본문에 클라이언트 자격 증명을 포함하는 것을 지원할 수 있습니다.

client_id
필수입니다. 섹션 2.2에 설명 된 등록 프로세스 중에 클라이언트에게 발급 된 클라이언트 식별자.

client_secret
필수입니다. 클라이언트 시크릿. 클라이언트 시크릿이 빈 문자열 인 경우 클라이언트는 매개 변수를 생략 할 수 있습니다.

두 매개 변수를 사용하여 요청 본문에 클라이언트 자격 증명을 포함하는 것은 권장되지 않으며 HTTP 기본 인증 체계 (또는 다른 암호 기반 HTTP 인증 체계)를 직접 사용할 수없는 클라이언트로 제한되어야합니다 (SHOULD). 매개 변수는 요청 본문에서만 전송 될 수 있으며 요청 URI에 포함되지 않아야합니다.


 예를 들어, 다음을 사용하여 액세스 토큰을 새로 고치는 요청 (섹션 6)
 본문 매개 변수 (표시 목적으로 추가 줄 바꿈 포함)
 뿐):

  POST /token HTTP/1.1
  Host: server.example.com
  Content-Type: application/x-www-form-urlencoded

  grant_type=refresh_token&refresh_token=tGzv3JOkF0XG5Qx2TlKWIA
  &client_id=s6BhdRkqt3&client_secret=7Fjfp0ZBr1KtDRbnfVdmIw


인증 서버는 암호 인증을 사용하여 요청을 보낼 때 1.6 절에 설명 된대로 TLS를 사용해야합니다. 이 클라이언트 인증 방법에는 암호가 포함되므로 권한 부여 서버는 무차별 대입 공격으로부터 암호를 사용하는 모든 엔드 포인트를 보호해야합니다.




2.3.2. Other Authentication Methods
The authorization server MAY support any suitable HTTP authentication scheme matching its security requirements. When using other authentication methods, the authorization server MUST define a mapping between the client identifier (registration record) and authentication scheme.


2.4. Unregistered Clients
This specification does not exclude the use of unregistered clients. However, the use of such clients is beyond the scope of this specification and requires additional security analysis and review of its interoperability impact.




3. Protocol Endpoints

The authorization process utilizes two authorization server endpoints (HTTP resources):

o Authorization endpoint - used by the client to obtain authorization from the resource owner via user-agent redirection.
o Token endpoint - used by the client to exchange an authorization grant for an access token, typically with client authentication.

As well as one client endpoint:
o Redirection endpoint - used by the authorization server to return responses containing authorization credentials to the client via the resource owner user-agent.

Not every authorization grant type utilizes both endpoints. Extension grant types MAY define additional endpoints as needed.



2.3.2. 기타 인증 방법
권한 부여 서버는 보안 요구 사항과 일치하는 적절한 HTTP 인증 체계를 지원할 수 있습니다. 다른 인증 방법을 사용할 때 권한 부여 서버는 클라이언트 식별자 (등록 레코드)와 인증 체계 간의 매핑을 정의해야합니다.


2.4. 등록되지 않은 클라이언트
이 사양은 등록되지 않은 클라이언트의 사용을 제외하지 않습니다. 그러나 이러한 클라이언트의 사용은이 사양의 범위를 벗어나며 추가 보안 분석 및 상호 운용성 영향에 대한 검토가 필요합니다.




3. 프로토콜 종점

권한 부여 프로세스는 두 개의 권한 부여 서버 끝점 (HTTP 리소스)을 사용합니다.

o 권한 부여 끝점-클라이언트가 사용자 에이전트 리디렉션을 통해 리소스 소유자로부터 권한을 얻기 위해 사용합니다.
o 토큰 끝점-클라이언트가 일반적으로 클라이언트 인증을 통해 액세스 토큰에 대한 권한 부여를 교환하는 데 사용됩니다.

하나의 클라이언트 엔드 포인트 :
o 리디렉션 끝점-인증 서버에서 리소스 소유자 사용자 에이전트를 통해 인증 자격 증명이 포함 된 응답을 클라이언트에 반환하는 데 사용됩니다.

모든 권한 부여 유형이 두 끝점을 모두 사용하는 것은 아닙니다. 확장 부여 유형은 필요에 따라 추가 엔드 포인트를 정의 할 수 있습니다.





3.1. Authorization Endpoint
The authorization endpoint is used to interact with the resource owner and obtain an authorization grant. The authorization server MUST first verify the identity of the resource owner. The way in which the authorization server authenticates the resource owner (e.g., username and password login, session cookies) is beyond the scope of this specification. The means through which the client obtains the location of the authorization endpoint are beyond the scope of this specification, but the location is typically provided in the service documentation. The endpoint URI MAY include an "application/x-www-form-urlencoded" formatted (per Appendix B) query component ([RFC3986] Section 3.4), which MUST be retained when adding additional query parameters. The endpoint URI MUST NOT include a fragment component. Since requests to the authorization endpoint result in user authentication and the transmission of clear-text credentials (in the HTTP response), the authorization server MUST require the use of TLS as described in Section 1.6 when sending requests to the authorization endpoint. The authorization server MUST support the use of the HTTP "GET" method [RFC2616] for the authorization endpoint and MAY support the use of the "POST" method as well.

Parameters sent without a value MUST be treated as if they were omitted from the request. The authorization server MUST ignore unrecognized request parameters. Request and response parameters MUST NOT be included more than once.

3.1.1. Response Type
The authorization endpoint is used by the authorization code grant type and implicit grant type flows. The client informs the authorization server of the desired grant type using the following parameter:

response_type
REQUIRED. The value MUST be one of "code" for requesting an authorization code as described by Section 4.1.1, "token" for requesting an access token (implicit grant) as described by Section 4.2.1, or a registered extension value as described by Section 8.4.




3.1. 승인 끝점
권한 부여 끝점은 리소스 소유자와 상호 작용하고 권한 부여를 얻는 데 사용됩니다. 권한 서버는 먼저 자원 소유자의 신원을 확인해야합니다. 권한 부여 서버가 리소스 소유자를 인증하는 방법 (예 : 사용자 이름 및 비밀번호 로그인, 세션 쿠키)은이 사양의 범위를 벗어납니다. 클라이언트가 권한 부여 끝점의 위치를 ​​얻는 방법은이 사양의 범위를 벗어나지 만 위치는 일반적으로 서비스 설명서에 제공됩니다. 엔드 포인트 URI는 추가 쿼리 매개 변수를 추가 할 때 유지되어야하는 (부록 B에 따라) 쿼리 구성 요소 ([RFC3986] 섹션 3.4) 형식의 "application / x-www-form-urlencoded"를 포함 할 수 있습니다. 끝점 URI는 조각 구성 요소를 포함하지 않아야합니다. 권한 부여 끝점에 대한 요청은 사용자 인증과 일반 텍스트 자격 증명 (HTTP 응답에서) 전송을 가져 오므로 권한 부여 서버는 권한 부여 끝점에 요청을 보낼 때 섹션 1.6에 설명 된대로 TLS를 사용해야합니다. 인증 서버는 인증 엔드 포인트에 대해 HTTP "GET"메소드 [RFC2616]의 사용을 지원해야하며 "POST"메소드의 사용도 지원할 수 있습니다.

값없이 전송 된 매개 변수는 요청에서 생략 된 것처럼 처리되어야합니다. 권한 부여 서버는 인식되지 않는 요청 매개 변수를 무시해야합니다. 요청 및 응답 매개 변수는 두 번 이상 포함되지 않아야합니다.

3.1.1. 응답 유형
권한 부여 끝점은 권한 부여 코드 부여 유형 및 암시 적 부여 유형 흐름에서 사용됩니다. 클라이언트는 다음 매개 변수를 사용하여 원하는 권한 부여 유형을 권한 부여 서버에 알립니다.

response_type
필수입니다. 값은 섹션 4.1.1에 설명 된대로 인증 코드를 요청하기위한 "코드", 섹션 4.2.1에 설명 된 액세스 토큰 (암시 적 허가)을 요청하기위한 "토큰"또는 다음에 설명 된 등록 된 확장 값 중 하나 여야합니다. 8.4 항.







Extension response types MAY contain a space-delimited (%x20) list of values, where the order of values does not matter (e.g., response type "a b" is the same as "b a"). The meaning of such composite response types is defined by their respective specifications. If an authorization request is missing the "response_type" parameter, or if the response type is not understood, the authorization server MUST return an error response as described in Section 4.1.2.1.

3.1.2. Redirection Endpoint
After completing its interaction with the resource owner, the authorization server directs the resource owner's user-agent back to the client. The authorization server redirects the user-agent to the client's redirection endpoint previously established with the authorization server during the client registration process or when making the authorization request. The redirection endpoint URI MUST be an absolute URI as defined by [RFC3986] Section 4.3. The endpoint URI MAY include an "application/x-www-form-urlencoded" formatted (per Appendix B) query component ([RFC3986] Section 3.4), which MUST be retained when adding additional query parameters. The endpoint URI MUST NOT include a fragment component.



3.1.2.1. Endpoint Request Confidentiality
The redirection endpoint SHOULD require the use of TLS as described in Section 1.6 when the requested response type is "code" or "token", or when the redirection request will result in the transmission of sensitive credentials over an open network. This specification does not mandate the use of TLS because at the time of this writing, requiring clients to deploy TLS is a significant hurdle for many client developers. If TLS is not available, the authorization server SHOULD warn the resource owner about the insecure endpoint prior to redirection (e.g., display a message during the authorization request). Lack of transport-layer security can have a severe impact on the security of the client and the protected resources it is authorized to access. The use of transport-layer security is particularly critical when the authorization process is used as a form of delegated end-user authentication by the client (e.g., third-party sign-in service).



확장 응답 유형은 값의 순서가 중요하지 않은 공백으로 구분 된 (% x20) 값 목록을 포함 할 수 있습니다 (예 : 응답 유형 "a b"는 "b a"와 동일 함). 이러한 복합 응답 유형의 의미는 해당 사양에 의해 정의됩니다. 인증 요청에 "response_type"매개 변수가 없거나 응답 유형이 이해되지 않는 경우 인증 서버는 섹션 4.1.2.1에 설명 된대로 오류 응답을 반환해야합니다.

3.1.2. 리디렉션 끝점
리소스 소유자와의 상호 작용을 완료 한 후 권한 부여 서버는 리소스 소유자의 사용자 에이전트를 다시 클라이언트로 보냅니다. 권한 부여 서버는 클라이언트 등록 프로세스 중 또는 권한 부여 요청을 할 때 이전에 권한 부여 서버에 설정된 클라이언트의 리디렉션 끝점으로 사용자 에이전트를 리디렉션합니다. 리디렉션 끝점 URI는 [RFC3986] 섹션 4.3에 정의 된대로 절대 URI 여야합니다. 엔드 포인트 URI는 추가 쿼리 매개 변수를 추가 할 때 유지되어야하는 (부록 B에 따라) 쿼리 구성 요소 ([RFC3986] 섹션 3.4) 형식의 "application / x-www-form-urlencoded"를 포함 할 수 있습니다. 끝점 URI는 조각 구성 요소를 포함하지 않아야합니다.



3.1.2.1. 엔드 포인트 요청 기밀성
리디렉션 엔드 포인트는 요청 된 응답 유형이 "코드"또는 "토큰"이거나 리디렉션 요청이 개방형 네트워크를 통해 민감한 자격 증명을 전송하게 될 때 섹션 1.6에 설명 된대로 TLS를 사용해야합니다 (SHOULD). 이 문서를 작성하는 시점에서 클라이언트에게 TLS를 배포하도록 요구하는 것은 많은 클라이언트 개발자에게 중요한 장애물이기 때문에이 사양은 TLS 사용을 의무화하지 않습니다. TLS를 사용할 수없는 경우 권한 부여 서버는 리디렉션 전에 안전하지 않은 끝점에 대해 리소스 소유자에게 경고해야합니다 (예 : 권한 부여 요청 중에 메시지 표시). 전송 계층 보안이 부족하면 클라이언트 및 액세스 권한이있는 보호 된 리소스의 보안에 심각한 영향을 미칠 수 있습니다. 전송 계층 보안의 사용은 권한 부여 프로세스가 클라이언트에 의해 위임 된 최종 사용자 인증의 한 형태로 사용될 때 특히 중요합니다 (예 : 타사 로그인 서비스).






3.1.2.2. Registration Requirements
The authorization server MUST require the following clients to register their redirection endpoint:

o Public clients.

o Confidential clients utilizing the implicit grant type.

The authorization server SHOULD require all clients to register their redirection endpoint prior to utilizing the authorization endpoint. The authorization server SHOULD require the client to provide the complete redirection URI (the client MAY use the "state" request parameter to achieve per-request customization). If requiring the registration of the complete redirection URI is not possible, the authorization server SHOULD require the registration of the URI scheme, authority, and path (allowing the client to dynamically vary only the query component of the redirection URI when requesting authorization). The authorization server MAY allow the client to register multiple redirection endpoints. Lack of a redirection URI registration requirement can enable an attacker to use the authorization endpoint as an open redirector as described in Section 10.15.



3.1.2.3. Dynamic Configuration
If multiple redirection URIs have been registered, if only part of the redirection URI has been registered, or if no redirection URI has been registered, the client MUST include a redirection URI with the authorization request using the "redirect_uri" request parameter. When a redirection URI is included in an authorization request, the authorization server MUST compare and match the value received against at least one of the registered redirection URIs (or URI components) as defined in [RFC3986] Section 6, if any redirection URIs were registered. If the client registration included the full redirection URI, the authorization server MUST compare the two URIs using simple string comparison as defined in [RFC3986] Section 6.2.1.



3.1.2.2. 등록 요건
권한 부여 서버는 리디렉션 끝점을 등록하기 위해 다음 클라이언트를 요구해야합니다.

o 공용 고객.

o 암시 적 허가 유형을 사용하는 기밀 클라이언트.

권한 부여 서버는 모든 클라이언트가 권한 부여 끝점을 사용하기 전에 리디렉션 끝점을 등록하도록 요구해야합니다 (SHOULD). 권한 부여 서버는 클라이언트가 완전한 리디렉션 URI를 제공하도록 요구해야합니다 (클라이언트는 요청 별 사용자 정의를 달성하기 위해 "state"요청 매개 변수를 사용할 수 있습니다). 완전한 리디렉션 URI의 등록을 요구할 수없는 경우 권한 부여 서버는 URI 체계, 권한 및 경로의 등록을 요구해야합니다 (권한을 요청할 때 클라이언트가 리디렉션 URI의 쿼리 구성 요소 만 동적으로 변경하도록 허용). 권한 부여 서버는 클라이언트가 여러 리디렉션 끝점을 등록하도록 허용 할 수 있습니다. 리디렉션 URI 등록 요구 사항이 없으면 공격자가 섹션 10.15에 설명 된대로 권한 부여 끝점을 개방형 리디렉터로 사용할 수 있습니다.



3.1.2.3. 동적 구성
여러 리디렉션 URI가 등록 된 경우, 리디렉션 URI의 일부만 등록되었거나 리디렉션 URI가 등록되지 않은 경우 클라이언트는 "redirect_uri"요청 매개 변수를 사용하는 인증 요청과 함께 리디렉션 URI를 포함해야합니다. 리디렉션 URI가 권한 요청에 포함 된 경우 권한 부여 서버는 리디렉션 URI가 등록 된 경우 [RFC3986] 섹션 6에 정의 된 등록 된 리디렉션 URI (또는 URI 구성 요소) 중 하나 이상과 수신 된 값을 비교하고 일치시켜야합니다. . 클라이언트 등록에 전체 리디렉션 URI가 포함 된 경우 권한 부여 서버는 [RFC3986] 섹션 6.2.1에 정의 된 간단한 문자열 비교를 사용하여 두 URI를 비교해야합니다.








3.1.2.4. Invalid Endpoint
If an authorization request fails validation due to a missing, invalid, or mismatching redirection URI, the authorization server SHOULD inform the resource owner of the error and MUST NOT automatically redirect the user-agent to the invalid redirection URI.

3.1.2.5. Endpoint Content
The redirection request to the client's endpoint typically results in an HTML document response, processed by the user-agent. If the HTML response is served directly as the result of the redirection request, any script included in the HTML document will execute with full access to the redirection URI and the credentials it contains. The client SHOULD NOT include any third-party scripts (e.g., third- party analytics, social plug-ins, ad networks) in the redirection endpoint response. Instead, it SHOULD extract the credentials from the URI and redirect the user-agent again to another endpoint without exposing the credentials (in the URI or elsewhere). If third-party scripts are included, the client MUST ensure that its own scripts (used to extract and remove the credentials from the URI) will execute first.


3.2. Token Endpoint
The token endpoint is used by the client to obtain an access token by presenting its authorization grant or refresh token. The token endpoint is used with every authorization grant except for the implicit grant type (since an access token is issued directly).


 The means through which the client obtains the location of the token endpoint are beyond the scope of this specification, but the location is typically provided in the service documentation. The endpoint URI MAY include an "application/x-www-form-urlencoded" formatted (per Appendix B) query component ([RFC3986] Section 3.4), which MUST be retained when adding additional query parameters. The endpoint URI MUST NOT include a fragment component. Since requests to the token endpoint result in the transmission of clear-text credentials (in the HTTP request and response), the authorization server MUST require the use of TLS as described in Section 1.6 when sending requests to the token endpoint. The client MUST use the HTTP "POST" method when making access token requests. Parameters sent without a value MUST be treated as if they were omitted from the request. The authorization server MUST ignore unrecognized request parameters. Request and response parameters MUST NOT be included more than once.


 3.1.2.4. 잘못된 끝점
권한 부여 요청이 누락되거나 유효하지 않거나 일치하지 않는 리디렉션 URI로 인해 유효성 검사에 실패하면 권한 부여 서버는 리소스 소유자에게 오류를 알려야하며 사용자 에이전트를 잘못된 리디렉션 URI로 자동 리디렉션해서는 안됩니다.

3.1.2.5. 엔드 포인트 콘텐츠
클라이언트의 끝점에 대한 리디렉션 요청은 일반적으로 사용자 에이전트가 처리하는 HTML 문서 응답을 생성합니다. HTML 응답이 리디렉션 요청의 결과로 직접 제공되는 경우 HTML 문서에 포함 된 모든 스크립트는 리디렉션 URI 및 포함 된 자격 증명에 대한 전체 액세스 권한으로 실행됩니다. 클라이언트는 리디렉션 엔드 포인트 응답에 타사 스크립트 (예 : 타사 분석, 소셜 플러그인, 광고 네트워크)를 포함하면 안됩니다. 대신 URI에서 자격 증명을 추출하고 자격 증명을 노출하지 않고 (URI 또는 ​​다른 곳에서) 사용자 에이전트를 다시 다른 끝점으로 리디렉션해야합니다. 타사 스크립트가 포함 된 경우 클라이언트는 자체 스크립트 (URI에서 자격 증명을 추출하고 제거하는 데 사용됨)가 먼저 실행되는지 확인해야합니다.


3.2. 토큰 엔드 포인트
토큰 끝점은 클라이언트가 권한 부여 또는 새로 고침 토큰을 제공하여 액세스 토큰을 얻는 데 사용됩니다. 토큰 끝점은 암시 적 권한 부여 유형을 제외한 모든 권한 부여와 함께 사용됩니다 (액세스 토큰이 직접 발급되기 때문에).


 클라이언트가 토큰 끝점의 위치를 ​​얻는 방법은이 사양의 범위를 벗어나지 만 위치는 일반적으로 서비스 설명서에 제공됩니다. 엔드 포인트 URI는 추가 쿼리 매개 변수를 추가 할 때 유지되어야하는 (부록 B에 따라) 쿼리 구성 요소 ([RFC3986] 섹션 3.4) 형식의 "application / x-www-form-urlencoded"를 포함 할 수 있습니다. 끝점 URI는 조각 구성 요소를 포함하지 않아야합니다. 토큰 끝점에 대한 요청은 일반 텍스트 자격 증명 (HTTP 요청 및 응답)을 전송하므로 권한 부여 서버는 요청을 토큰 끝점에 보낼 때 섹션 1.6에 설명 된대로 TLS를 사용해야합니다. 클라이언트는 액세스 토큰 요청을 할 때 반드시 HTTP "POST"메소드를 사용해야합니다. 값없이 전송 된 매개 변수는 요청에서 생략 된 것처럼 처리되어야합니다. 권한 부여 서버는 인식되지 않는 요청 매개 변수를 무시해야합니다. 요청 및 응답 매개 변수는 두 번 이상 포함되지 않아야합니다.










 3.2.1. Client Authentication
 Confidential clients or other clients issued client credentials MUST authenticate with the authorization server as described in Section 2.3 when making requests to the token endpoint. Client authentication is used for:

 o Enforcing the binding of refresh tokens and authorization codes to the client they were issued to. Client authentication is critical when an authorization code is transmitted to the redirection endpoint over an insecure channel or when the redirection URI has not been registered in full.

 o Recovering from a compromised client by disabling the client or changing its credentials, thus preventing an attacker from abusing stolen refresh tokens. Changing a single set of client credentials is significantly faster than revoking an entire set of refresh tokens.

 o Implementing authentication management best practices, which require periodic credential rotation. Rotation of an entire set of refresh tokens can be challenging, while rotation of a single set of client credentials is significantly easier.


 A client MAY use the "client_id" request parameter to identify itself when sending requests to the token endpoint. In the "authorization_code" "grant_type" request to the token endpoint, an unauthenticated client MUST send its "client_id" to prevent itself from inadvertently accepting a code intended for a client with a different "client_id". This protects the client from substitution of the authentication code. (It provides no additional security for the protected resource.)




 3.2.1. 클라이언트 인증
 기밀 클라이언트 또는 기타 클라이언트가 발급 한 클라이언트 자격 증명은 토큰 엔드 포인트에 요청할 때 2.3 절에 설명 된대로 인증 서버로 인증해야합니다. 클라이언트 인증은 다음에 사용됩니다.

 o 발급 된 클라이언트에 대한 새로 고침 토큰 및 권한 부여 코드 바인딩 적용. 클라이언트 인증은 인증 코드가 안전하지 않은 채널을 통해 리디렉션 끝점으로 전송되거나 리디렉션 URI가 완전히 등록되지 않은 경우에 중요합니다.

 o 클라이언트를 비활성화하거나 자격 증명을 변경하여 손상된 클라이언트에서 복구하여 공격자가 훔친 새로 고침 토큰을 남용하지 못하도록합니다. 단일 클라이언트 자격 증명 집합을 변경하는 것이 전체 새로 고침 토큰 집합을 취소하는 것보다 훨씬 빠릅니다.

 o 정기적 인 자격 증명 교체가 필요한 인증 관리 모범 사례를 구현합니다. 전체 새로 고침 토큰 집합을 교체하는 것은 어려울 수 있지만 단일 클라이언트 자격 증명 집합을 교체하는 것은 훨씬 쉽습니다.


 클라이언트는 "client_id"요청 매개 변수를 사용하여 토큰 끝점에 요청을 보낼 때 자신을 식별 할 수 있습니다. 토큰 엔드 포인트에 대한 "authorization_code" "grant_type"요청에서 인증되지 않은 클라이언트는 "client_id"를 전송하여 다른 "client_id"를 가진 클라이언트 용 코드를 실수로 받아들이지 않도록해야합니다. 이것은 인증 코드의 대체로부터 클라이언트를 보호합니다. (보호 된 리소스에 대한 추가 보안을 제공하지 않습니다.)











 3.3. Access Token Scope
 The authorization and token endpoints allow the client to specify the scope of the access request using the "scope" request parameter. In turn, the authorization server uses the "scope" response parameter to inform the client of the scope of the access token issued. The value of the scope parameter is expressed as a list of space- delimited, case-sensitive strings. The strings are defined by the authorization server. If the value contains multiple space-delimited strings, their order does not matter, and each string adds an additional access range to the requested scope.

 scope = scope-token *( SP scope-token )
 scope-token = 1*( %x21 / %x23-5B / %x5D-7E )

 The authorization server MAY fully or partially ignore the scope requested by the client, based on the authorization server policy or the resource owner's instructions. If the issued access token scope is different from the one requested by the client, the authorization server MUST include the "scope" response parameter to inform the client of the actual scope granted. If the client omits the scope parameter when requesting authorization, the authorization server MUST either process the request using a pre-defined default value or fail the request indicating an invalid scope. The authorization server SHOULD document its scope requirements and default value (if defined).

 4. Obtaining Authorization
 To request an access token, the client obtains authorization from the resource owner. The authorization is expressed in the form of an authorization grant, which the client uses to request the access token. OAuth defines four grant types: authorization code, implicit, resource owner password credentials, and client credentials. It also provides an extension mechanism for defining additional grant types.



3.3. 액세스 토큰 범위
 권한 부여 및 토큰 끝점을 통해 클라이언트는 "scope"요청 매개 변수를 사용하여 액세스 요청의 범위를 지정할 수 있습니다. 차례로 권한 부여 서버는 "scope"응답 매개 변수를 사용하여 발급 된 액세스 토큰의 범위를 클라이언트에 알립니다. 범위 매개 변수의 값은 공백으로 구분되고 대소 문자를 구분하는 문자열 목록으로 표현됩니다. 문자열은 권한 부여 서버에 의해 정의됩니다. 값에 공백으로 구분 된 여러 문자열이 포함 된 경우 순서는 중요하지 않으며 각 문자열은 요청 된 범위에 추가 액세스 범위를 추가합니다.

 scope = scope-token *( SP scope-token )
 scope-token = 1*( %x21 / %x23-5B / %x5D-7E )

 권한 부여 서버는 권한 부여 서버 정책 또는 자원 소유자의 지시에 따라 클라이언트가 요청한 범위를 완전히 또는 부분적으로 무시할 수 있습니다. 발급 된 액세스 토큰 범위가 클라이언트가 요청한 범위와 다른 경우 권한 부여 서버는 "범위"응답 매개 변수를 포함하여 클라이언트에게 부여 된 실제 범위를 알려야합니다. 클라이언트가 권한을 요청할 때 범위 매개 변수를 생략하면 권한 서버는 미리 정의 된 기본값을 사용하여 요청을 처리하거나 유효하지 않은 범위를 나타내는 요청을 실패해야합니다. 권한 부여 서버는 범위 요구 사항과 기본값 (정의 된 경우)을 문서화해야합니다 (SHOULD).

 4. 승인 받기
 액세스 토큰을 요청하기 위해 클라이언트는 리소스 소유자로부터 권한을 얻습니다. 권한은 클라이언트가 액세스 토큰을 요청하는 데 사용하는 권한 부여의 형태로 표현됩니다. OAuth는 인증 코드, 암시 적, 리소스 소유자 암호 자격 증명 및 클라이언트 자격 증명의 네 가지 부여 유형을 정의합니다. 또한 추가 부여 유형을 정의하기위한 확장 메커니즘을 제공합니다.











4.1. Authorization Code Grant
The authorization code grant type is used to obtain both access tokens and refresh tokens and is optimized for confidential clients. Since this is a redirection-based flow, the client must be capable of interacting with the resource owner's user-agent (typically a web browser) and capable of receiving incoming requests (via redirection) from the authorization server.


4.1. 인증 코드 부여
인증 코드 부여 유형은 액세스 토큰과 새로 고침 토큰을 모두 얻는 데 사용되며 기밀 클라이언트에 최적화되어 있습니다. 이것은 리디렉션 기반 흐름이므로 클라이언트는 리소스 소유자의 사용자 에이전트 (일반적으로 웹 브라우저)와 상호 작용할 수 있어야하며 권한 부여 서버에서 들어오는 요청 (리디렉션을 통해)을받을 수 있어야합니다.


+----------+
| Resource |
|   Owner  |
|          |
+----------+
     ^
     |
    (B)
+----|-----+          Client Identifier      +---------------+
|         -+----(A)-- & Redirection URI ---->|               |
|  User-   |                                 | Authorization |
|  Agent  -+----(B)-- User authenticates --->|     Server    |
|          |                                 |               |
|         -+----(C)-- Authorization Code ---<|               |
+-|----|---+                                 +---------------+
  |    |                                         ^      v
 (A)  (C)                                        |      |
  |    |                                         |      |
  ^    v                                         |      |
+---------+                                      |      |
|         |>---(D)-- Authorization Code ---------'      |
|  Client |          & Redirection URI                  |
|         |                                             |
|         |<---(E)----- Access Token -------------------'
+---------+       (w/ Optional Refresh Token)

Note: The lines illustrating steps (A), (B), and (C) are broken into
two parts as they pass through the user-agent.

                Figure 3: Authorization Code Flow


참고 : 단계 (A), (B) 및 (C)를 설명하는 선은
  사용자 에이전트를 통과하는 두 부분.

     그림 3 : 인증 코드 흐름




 The flow illustrated in Figure 3 includes the following steps: (A) The client initiates the flow by directing the resource owner's user-agent to the authorization endpoint. The client includes its client identifier, requested scope, local state, and a redirection URI to which the authorization server will send the user-agent back once access is granted (or denied). (B) The authorization server authenticates the resource owner (via the user-agent) and establishes whether the resource owner grants or denies the client's access request. (C) Assuming the resource owner grants access, the authorization server redirects the user-agent back to the client using the redirection URI provided earlier (in the request or during client registration). The redirection URI includes an authorization code and any local state provided by the client earlier. (D) The client requests an access token from the authorization server's token endpoint by including the authorization code received in the previous step. When making the request, the client authenticates with the authorization server. The client includes the redirection URI used to obtain the authorization code for verification. (E) The authorization server authenticates the client, validates the authorization code, and ensures that the redirection URI received matches the URI used to redirect the client in step (C). If valid, the authorization server responds back with an access token and, optionally, a refresh token.


 그림 3에 표시된 흐름에는 다음 단계가 포함됩니다. (A) 클라이언트는 리소스 소유자의 사용자 에이전트를 권한 부여 끝점으로 보내 흐름을 시작합니다. 클라이언트에는 클라이언트 식별자, 요청 된 범위, 로컬 상태 및 액세스가 허용 (또는 거부)되면 권한 부여 서버가 사용자 에이전트를 다시 보낼 리디렉션 URI가 포함됩니다. (B) 권한 부여 서버는 사용자 에이전트를 통해 리소스 소유자를 인증하고 리소스 소유자가 클라이언트의 액세스 요청을 허용 또는 거부할지 여부를 설정합니다. (C) 리소스 소유자가 액세스 권한을 부여한다고 가정하면 권한 부여 서버는 이전에 제공된 리디렉션 URI를 사용하여 사용자 에이전트를 클라이언트로 다시 리디렉션합니다 (요청시 또는 클라이언트 등록 중에). 리디렉션 URI에는 인증 코드와 이전에 클라이언트에서 제공 한 모든 로컬 상태가 포함됩니다. (D) 클라이언트는 이전 단계에서받은 인증 코드를 포함하여 인증 서버의 토큰 끝점에서 액세스 토큰을 요청합니다. 요청을 할 때 클라이언트는 권한 부여 서버로 인증합니다. 클라이언트에는 확인을위한 인증 코드를 얻는 데 사용되는 리디렉션 URI가 포함됩니다. (E) 인증 서버는 클라이언트를 인증하고 인증 코드의 유효성을 검사하며 수신 된 리디렉션 URI가 (C) 단계에서 클라이언트를 리디렉션하는 데 사용 된 URI와 일치하는지 확인합니다. 유효한 경우 권한 부여 서버는 액세스 토큰 및 선택적으로 새로 고침 토큰으로 응답합니다.









 4.1.1. Authorization Request
 The client constructs the request URI by adding the following parameters to the query component of the authorization endpoint URI using the "application/x-www-form-urlencoded" format, per Appendix B:

 response_type
 REQUIRED. Value MUST be set to "code".


 client_id
 REQUIRED. The client identifier as described in Section 2.2.

 redirect_uri
 OPTIONAL. As described in Section 3.1.2.


 scope
 OPTIONAL. The scope of the access request as described by Section 3.3.


 state
 RECOMMENDED. An opaque value used by the client to maintain state between the request and callback. The authorization server includes this value when redirecting the user-agent back to the client. The parameter SHOULD be used for preventing cross-site request forgery as described in Section 10.12.





 4.1.1. 승인 요청
  클라이언트는 부록 B에 따라 "application / x-www-form-urlencoded"형식을 사용하여 권한 부여 끝점 URI의 쿼리 구성 요소에 다음 매개 변수를 추가하여 요청 URI를 구성합니다.

  response_type
  필수입니다. 값은 "code"로 설정되어야합니다.


  client_id
  필수입니다. 섹션 2.2에 설명 된 클라이언트 식별자.

  redirect_uri
  선택 과목. 섹션 3.1.2에 설명 된대로.


  scope
  선택 과목. 섹션 3.3에 설명 된 액세스 요청의 범위.


  state
  권장합니다. 요청과 콜백 사이의 상태를 유지하기 위해 클라이언트가 사용하는 불투명 한 값입니다. 권한 부여 서버는 사용자 에이전트를 클라이언트로 다시 리디렉션 할 때이 값을 포함합니다. 매개 변수는 섹션 10.12에 설명 된대로 교차 사이트 요청 위조를 방지하기 위해 사용되어야합니다 (SHOULD).









 The client directs the resource owner to the constructed URI using an HTTP redirection response, or by other means available to it via the user-agent.

 For example, the client directs the user-agent to make the following HTTP request using TLS (with extra line breaks for display purposes only):

 GET /authorize?response_type=code&client_id=s6BhdRkqt3&state=xyz &redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb HTTP/1.1
 Host: server.example.com


 The authorization server validates the request to ensure that all required parameters are present and valid. If the request is valid, the authorization server authenticates the resource owner and obtains an authorization decision (by asking the resource owner or by establishing approval via other means). When a decision is established, the authorization server directs the user-agent to the provided client redirection URI using an HTTP redirection response, or by other means available to it via the user-agent.


클라이언트는 HTTP 리디렉션 응답을 사용하거나 사용자 에이전트를 통해 사용할 수있는 다른 방법을 사용하여 리소스 소유자를 구성된 URI로 보냅니다.

  예를 들어 클라이언트는 TLS를 사용하여 다음 HTTP 요청을 수행하도록 사용자 에이전트에 지시합니다 (표시 목적으로 만 추가 줄 바꿈 포함).

 GET /authorize?response_type=code&client_id=s6BhdRkqt3&state=xyz &redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb HTTP/1.1
 Host: server.example.com

  권한 서버는 모든 필수 매개 변수가 존재하고 유효한지 확인하기 위해 요청의 유효성을 검증합니다. 요청이 유효하면 권한 서버는 리소스 소유자를 인증하고 권한 결정을 얻습니다 (리소스 소유자에게 요청하거나 다른 방법을 통해 승인을 설정하여). 결정이 내려지면 권한 부여 서버는 HTTP 리디렉션 응답을 사용하거나 사용자 에이전트를 통해 사용할 수있는 다른 수단을 사용하여 제공된 클라이언트 리디렉션 URI로 사용자 에이전트를 보냅니다.













 4.1.2. Authorization Response
 If the resource owner grants the access request, the authorization server issues an authorization code and delivers it to the client by adding the following parameters to the query component of the redirection URI using the "application/x-www-form-urlencoded" format, per Appendix B:

 code
 REQUIRED. The authorization code generated by the authorization server. The authorization code MUST expire shortly after it is issued to mitigate the risk of leaks. A maximum authorization code lifetime of 10 minutes is

 RECOMMENDED. The client MUST NOT use the authorization code more than once. If an authorization code is used more than once, the authorization server MUST deny the request and SHOULD revoke (when possible) all tokens previously issued based on that authorization code. The authorization code is bound to the client identifier and redirection URI.


 state
 REQUIRED if the "state" parameter was present in the client authorization request. The exact value received from the client.

 For example, the authorization server redirects the user-agent by sending the following

 HTTP response: HTTP/1.1 302 Found
 Location: https://client.example.com/cb?code=SplxlOBeZQQYbYS6WxSbIA&state=xyz

 The client MUST ignore unrecognized response parameters. The authorization code string size is left undefined by this specification. The client should avoid making assumptions about code value sizes. The authorization server SHOULD document the size of any value it issues.



 4.1.2. 승인 응답
 리소스 소유자가 액세스 요청을 허용하면 권한 부여 서버는 "application / x-www-form-urlencoded"형식을 사용하여 리디렉션 URI의 쿼리 구성 요소에 다음 매개 변수를 추가하여 권한 코드를 발급하고 클라이언트에 전달합니다. 부록 B에 따라 :

 code
 필수입니다. 권한 서버에서 생성 된 권한 코드입니다. 인증 코드는 누출 위험을 줄이기 위해 발급 된 직후 만료되어야합니다. 최대 인증 코드 수명은 10 분입니다.

 권장합니다. 클라이언트는 인증 코드를 두 번 이상 사용해서는 안됩니다. 인증 코드가 두 번 이상 사용되는 경우 인증 서버는 요청을 거부해야하며 해당 인증 코드를 기반으로 이전에 발행 된 모든 토큰을 취소해야합니다 (가능한 경우). 인증 코드는 클라이언트 식별자 및 리디렉션 URI에 바인딩됩니다.


 state
 클라이언트 권한 요청에 "state"매개 변수가있는 경우 필수입니다. 클라이언트로부터받은 정확한 값입니다.

 예를 들어 권한 부여 서버는 다음을 전송하여 사용자 에이전트를 리디렉션합니다.

HTTP response: HTTP/1.1 302 Found
 Location: https://client.example.com/cb?code=SplxlOBeZQQYbYS6WxSbIA&state=xyz

 클라이언트는 인식되지 않는 응답 매개 변수를 무시해야합니다. 인증 코드 문자열 크기는이 사양에 정의되지 않은 상태로 남아 있습니다. 클라이언트는 코드 값 크기에 대한 가정을 피해야합니다. 권한 부여 서버는 발행하는 값의 크기를 문서화해야합니다.











 4.1.2.1. Error Response

 If the request fails due to a missing, invalid, or mismatching redirection URI, or if the client identifier is missing or invalid, the authorization server SHOULD inform the resource owner of the error and MUST NOT automatically redirect the user-agent to the invalid redirection URI. If the resource owner denies the access request or if the request fails for reasons other than a missing or invalid redirection URI, the authorization server informs the client by adding the following parameters to the query component of the redirection URI using the "application/x-www-form-urlencoded" format, per Appendix B:

 error
 REQUIRED. A single ASCII [USASCII] error code from the following:

       invalid_request  
       The request is missing a required parameter, includes an invalid parameter value, includes a parameter more than once, or is otherwise malformed.

       unauthorized_client  
       The client is not authorized to request an authorization code using this method.

       access_denied  
       The resource owner or authorization server denied the request.

       unsupported_response_type  
       The authorization server does not support obtaining an authorization code using this method.

       invalid_scope  
       The requested scope is invalid, unknown, or malformed.


        server_error  
        The authorization server encountered an unexpected condition that prevented it from fulfilling the request. (This error code is needed because a 500 Internal Server  Error HTTP status code cannot be returned to the client  via an HTTP redirect.)

        temporarily_unavailable
        The authorization server is currently unable to handle the request due to a temporary overloading or maintenance of the server. (This error code is needed because a 503 Service Unavailable HTTP status code cannot be returned to the client via an HTTP redirect.)

        Values for the "error" parameter MUST NOT include characters outside the set %x20-21 / %x23-5B / %x5D-7E.




 4.1.2.1. 오류 응답

 요청이 누락, 유효하지 않거나 일치하지 않는 리디렉션 URI로 인해 실패하거나 클라이언트 식별자가 누락되거나 유효하지 않은 경우 권한 부여 서버는 리소스 소유자에게 오류를 알려야하며 사용자 에이전트를 잘못된 리디렉션으로 자동 리디렉션해서는 안됩니다. URI. 리소스 소유자가 액세스 요청을 거부하거나 누락되거나 잘못된 리디렉션 URI 이외의 이유로 요청이 실패하는 경우 권한 부여 서버는 "application / x-"를 사용하여 리디렉션 URI의 쿼리 구성 요소에 다음 매개 변수를 추가하여 클라이언트에 알립니다. 부록 B에 따른 www-form-urlencoded "형식 :


 error
 필수입니다. 다음의 단일 ASCII [USASCII] 오류 코드 :

       invalid_request
       요청에 필수 매개 변수가 누락되었거나 유효하지 않은 매개 변수 값이 포함되어 있거나 매개 변수가 두 번 이상 포함되어 있거나 형식이 잘못되었습니다.

       unauthorized_client
       클라이언트는이 방법을 사용하여 인증 코드를 요청할 권한이 없습니다.

       access_denied
       자원 소유자 또는 권한 부여 서버가 요청을 거부했습니다.

       unsupported_response_type
       권한 서버는이 방법을 사용한 권한 코드 획득을 지원하지 않습니다.

       invalid_scope
       요청한 범위가 잘못되었거나 알 수 없거나 형식이 잘못되었습니다.

        server_error
        권한 부여 서버가 요청을 이행하지 못하게하는 예기치 않은 조건을 발견했습니다. (이 오류 코드는 500 내부 서버 오류 HTTP 상태 코드를 HTTP 리디렉션을 통해 클라이언트로 반환 할 수 없기 때문에 필요합니다.)

        temporarily_unavailable
        권한 서버는 현재 서버의 일시적인 과부하 또는 유지 보수로 인해 요청을 처리 할 수 ​​없습니다. (이 오류 코드는 503 Service Unavailable HTTP 상태 코드를 HTTP 리디렉션을 통해 클라이언트에 반환 할 수 없기 때문에 필요합니다.)

        "오류"매개 변수의 값은 %x20-21 / %x23-5B / %x5D-7E 세트 외부의 문자를 포함하면 안됩니다 (MUST NOT).






 error_description
 OPTIONAL. Human-readable ASCII [USASCII] text providing additional information, used to assist the client developer in understanding the error that occurred. Values for the "error_description" parameter MUST NOT include characters outside the set %x20-21 / %x23-5B / %x5D-7E.

 error_uri
 OPTIONAL. A URI identifying a human-readable web page with information about the error, used to provide the client developer with additional information about the error. Values for the "error_uri" parameter MUST conform to the URI-reference syntax and thus MUST NOT include characters outside the set %x21 / %x23-5B / %x5D-7E.


 state
 REQUIRED if a "state" parameter was present in the client authorization request. The exact value received from the client.

 For example, the authorization server redirects the user-agent by sending the following HTTP response:

 HTTP/1.1 302 Found
 Location: https://client.example.com/cb?error=access_denied&state=xyz



 error_description
 선택 과목. 클라이언트 개발자가 발생한 오류를 이해하는 데 사용되는 추가 정보를 제공하는 사람이 읽을 수있는 ASCII [USASCII] 텍스트입니다. "error_description"매개 변수의 값은 %x20-21 / %x23-5B / %x5D-7E 세트 외부의 문자를 포함하면 안됩니다 (MUST NOT).

 error_uri
 선택 과목. 오류에 대한 정보가있는 사람이 읽을 수있는 웹 페이지를 식별하는 URI로, 클라이언트 개발자에게 오류에 대한 추가 정보를 제공하는 데 사용됩니다. "error_uri"매개 변수의 값은 URI 참조 구문을 준수해야하며 따라서 %x21 / %x23-5B / %x5D-7E 세트 외부의 문자를 포함하면 안됩니다.


 state
 클라이언트 인증 요청에 "state"매개 변수가있는 경우 필수입니다. 클라이언트로부터받은 정확한 값입니다.

 예를 들어 권한 부여 서버는 다음 HTTP 응답을 전송하여 사용자 에이전트를 리디렉션합니다.


 HTTP/1.1 302 Found
 Location: https://client.example.com/cb?error=access_denied&state=xyz
















 4.1.3. Access Token
 Request The client makes a request to the token endpoint by sending the following parameters using the "application/x-www-form-urlencoded" format per Appendix B with a character encoding of UTF-8 in the HTTP request entity-body:

 grant_type
 REQUIRED. Value MUST be set to "authorization_code".

 code
 REQUIRED. The authorization code received from the authorization server.


 redirect_uri
 REQUIRED, if the "redirect_uri" parameter was included in the authorization request as described in Section 4.1.1, and their values MUST be identical.

 client_id
 REQUIRED, if the client is not authenticating with the authorization server as described in Section 3.2.1.

 If the client type is confidential or the client was issued client credentials (or assigned other authentication requirements), the client MUST authenticate with the authorization server as described in Section 3.2.1.

 For example, the client makes the following HTTP request using TLS (with extra line breaks for display purposes only):

 POST /token HTTP/1.1
 Host: server.example.com
 Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
 Content-Type: application/x-www-form-urlencoded
 grant_type=authorization_code&code=SplxlOBeZQQYbYS6WxSbIA &redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb


 4.1.3. 액세스 토큰
 요청 클라이언트는 HTTP 요청 엔티티 본문에서 UTF-8 문자 인코딩과 함께 부록 B에 따라 "application / x-www-form-urlencoded"형식을 사용하여 다음 매개 변수를 전송하여 토큰 엔드 포인트에 요청합니다.

 grant_type
 필수입니다. 값은 "authorization_code"로 설정해야합니다.

 code
 필수입니다. 인증 서버에서받은 인증 코드입니다.


 redirect_uri
 필수, "redirect_uri"매개 변수가 섹션 4.1.1에 설명 된대로 권한 부여 요청에 포함되었으며 해당 값이 동일해야합니다.

 client_id
 클라이언트가 섹션 3.2.1에 설명 된대로 권한 부여 서버로 인증하지 않는 경우 필수입니다.

 클라이언트 유형이 기밀이거나 클라이언트가 클라이언트 자격 증명 (또는 다른 인증 요구 사항이 할당 됨)이 발급 된 경우 클라이언트는 섹션 3.2.1에 설명 된대로 인증 서버로 인증해야합니다.

 예를 들어 클라이언트는 TLS를 사용하여 다음 HTTP 요청을 수행합니다 (표시 목적으로 만 추가 줄 바꿈 포함).

POST /token HTTP/1.1
 Host: server.example.com
 Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
 Content-Type: application/x-www-form-urlencoded
  grant_type=authorization_code&code=SplxlOBeZQQYbYS6WxSbIA &redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb





 The authorization server MUST: o require client authentication for confidential clients or for any client that was issued client credentials (or with other authentication requirements), o authenticate the client if client authentication is included, o ensure that the authorization code was issued to the authenticated confidential client, or if the client is public, ensure that the code was issued to "client_id" in the request, o verify that the authorization code is valid, and o ensure that the "redirect_uri" parameter is present if the "redirect_uri" parameter was included in the initial authorization request as described in Section 4.1.1, and if included ensure that their values are identical.

 4.1.4. Access Token Response
 If the access token request is valid and authorized, the authorization server issues an access token and optional refresh token as described in Section 5.1. If the request client authentication failed or is invalid, the authorization server returns an error response as described in Section 5.2.


 An example successful response:

 HTTP/1.1 200 OK
 Content-Type: application/json;charset=UTF-8
 Cache-Control: no-store
 Pragma: no-cache
 {
 "access_token":"2YotnFZFEjr1zCsicMWpAA",
 "token_type":"example",
 "expires_in":3600,
 "refresh_token":"tGzv3JOkF0XG5Qx2TlKWIA",
 "example_parameter":"example_value" }



 권한 부여 서버는 다음을 수행해야합니다. o 기밀 클라이언트 또는 클라이언트 자격 증명 (또는 기타 인증 요구 사항)이 발급 된 모든 클라이언트에 대해 클라이언트 인증을 요구하고, o 클라이언트 인증이 포함 된 경우 클라이언트를 인증하고, o 인증 코드가 인증 된 사용자에게 발급되었는지 확인합니다. 기밀 클라이언트이거나 클라이언트가 공용 인 경우 요청에서 "client_id"에 코드가 발행되었는지 확인하고, o 인증 코드가 유효한지 확인하고, o "redirect_uri"매개 변수가있는 경우 "redirect_uri"매개 변수가 있는지 확인합니다. 섹션 4.1.1에 설명 된대로 초기 승인 요청에 포함되었으며 포함 된 경우 해당 값이 동일한 지 확인하십시오.

 4.1.4. 액세스 토큰 응답
 액세스 토큰 요청이 유효하고 승인 된 경우 권한 부여 서버는 섹션 5.1에 설명 된대로 액세스 토큰과 선택적 새로 고침 토큰을 발급합니다. 요청 클라이언트 인증이 실패하거나 유효하지 않은 경우 권한 부여 서버는 섹션 5.2에 설명 된대로 오류 응답을 반환합니다.


 성공적인 응답의 예 :

 HTTP/1.1 200 OK
 Content-Type: application/json;charset=UTF-8
 Cache-Control: no-store
 Pragma: no-cache
 {
 "access_token":"2YotnFZFEjr1zCsicMWpAA",
 "token_type":"example",
 "expires_in":3600,
 "refresh_token":"tGzv3JOkF0XG5Qx2TlKWIA",
 "example_parameter":"example_value" }













 4.2. Implicit Grant
 The implicit grant type is used to obtain access tokens (it does not support the issuance of refresh tokens) and is optimized for public clients known to operate a particular redirection URI. These clients are typically implemented in a browser using a scripting language such as JavaScript. Since this is a redirection-based flow, the client must be capable of interacting with the resource owner's user-agent (typically a web browser) and capable of receiving incoming requests (via redirection) from the authorization server. Unlike the authorization code grant type, in which the client makes separate requests for authorization and for an access token, the client receives the access token as the result of the authorization request. The implicit grant type does not include client authentication, and relies on the presence of the resource owner and the registration of the redirection URI. Because the access token is encoded into the redirection URI, it may be exposed to the resource owner and other applications residing on the same device.

4.2. 암시 적 부여
 암시 적 권한 부여 유형은 액세스 토큰을 얻는 데 사용되며 (새로 고침 토큰 발급을 지원하지 않음) 특정 리디렉션 URI를 작동하는 것으로 알려진 공용 클라이언트에 최적화됩니다. 이러한 클라이언트는 일반적으로 JavaScript와 같은 스크립팅 언어를 사용하는 브라우저에서 구현됩니다. 이것은 리디렉션 기반 흐름이므로 클라이언트는 리소스 소유자의 사용자 에이전트 (일반적으로 웹 브라우저)와 상호 작용할 수 있어야하며 권한 부여 서버에서 들어오는 요청 (리디렉션을 통해)을받을 수 있어야합니다. 클라이언트가 별도의 인증 요청과 액세스 토큰을 요청하는 인증 코드 부여 유형과 달리 클라이언트는 인증 요청의 결과로 액세스 토큰을받습니다. 암시 적 권한 부여 유형은 클라이언트 인증을 포함하지 않으며 리소스 소유자의 존재와 리디렉션 URI의 등록에 의존합니다. 액세스 토큰은 리디렉션 URI로 인코딩되기 때문에 리소스 소유자 및 동일한 장치에있는 다른 응용 프로그램에 노출 될 수 있습니다.


     +----------+
     | Resource |
     |  Owner   |
     |          |
     +----------+
          ^
          |
         (B)
     +----|-----+          Client Identifier     +---------------+
     |         -+----(A)-- & Redirection URI --->|               |
     |  User-   |                                | Authorization |
     |  Agent  -|----(B)-- User authenticates -->|     Server    |
     |          |                                |               |
     |          |<---(C)--- Redirection URI ----<|               |
     |          |          with Access Token     +---------------+
     |          |            in Fragment
     |          |                                +---------------+
     |          |----(D)--- Redirection URI ---->|   Web-Hosted  |
     |          |          without Fragment      |     Client    |
     |          |                                |    Resource   |
     |     (F)  |<---(E)------- Script ---------<|               |
     |          |                                +---------------+
     +-|--------+
       |    |
      (A)  (G) Access Token
       |    |
       ^    v
     +---------+
     |         |
     |  Client |
     |         |
     +---------+

   Note: The lines illustrating steps (A) and (B) are broken into two
   parts as they pass through the user-agent.

                       Figure 4: Implicit Grant Flow

참고 : 단계 (A)와 (B)를 나타내는 선은 두 개로 나뉩니다.
    사용자 에이전트를 통과하는 부분.

                        그림 4 : 암시 적 허가 흐름


 The flow illustrated in Figure 4 includes the following steps: (A) The client initiates the flow by directing the resource owner's user-agent to the authorization endpoint. The client includes its client identifier, requested scope, local state, and a redirection URI to which the authorization server will send the user-agent back once access is granted (or denied). (B) The authorization server authenticates the resource owner (via the user-agent) and establishes whether the resource owner grants or denies the client's access request. (C) Assuming the resource owner grants access, the authorization server redirects the user-agent back to the client using the redirection URI provided earlier. The redirection URI includes the access token in the URI fragment. (D) The user-agent follows the redirection instructions by making a request to the web-hosted client resource (which does not include the fragment per [RFC2616]). The user-agent retains the fragment information locally. (E) The web-hosted client resource returns a web page (typically an HTML document with an embedded script) capable of accessing the full redirection URI including the fragment retained by the user-agent, and extracting the access token (and other parameters) contained in the fragment. (F) The user-agent executes the script provided by the web-hosted client resource locally, which extracts the access token. (G) The user-agent passes the access token to the client. See Sections 1.3.2 and 9 for background on using the implicit grant. See Sections 10.3 and 10.16 for important security considerations when using the implicit grant.

 그림 4에 표시된 흐름에는 다음 단계가 포함됩니다. (A) 클라이언트는 리소스 소유자의 사용자 에이전트를 권한 부여 끝점으로 보내 흐름을 시작합니다. 클라이언트에는 클라이언트 식별자, 요청 된 범위, 로컬 상태 및 액세스가 허용 (또는 거부)되면 권한 부여 서버가 사용자 에이전트를 다시 보낼 리디렉션 URI가 포함됩니다. (B) 권한 부여 서버는 사용자 에이전트를 통해 리소스 소유자를 인증하고 리소스 소유자가 클라이언트의 액세스 요청을 허용 또는 거부할지 여부를 설정합니다. (C) 리소스 소유자가 액세스 권한을 부여한다고 가정하면 권한 부여 서버는 이전에 제공된 리디렉션 URI를 사용하여 사용자 에이전트를 클라이언트로 다시 리디렉션합니다. 리디렉션 URI는 URI 조각에 액세스 토큰을 포함합니다. (D) 사용자 에이전트는 웹 호스팅 클라이언트 리소스 ([RFC2616]에 따른 조각을 포함하지 않음)에 리디렉션 지침에 따라 요청을 만듭니다. 사용자 에이전트는 조각 정보를 로컬로 유지합니다. (E) 웹 호스팅 클라이언트 리소스는 사용자 에이전트가 보유한 조각을 포함하여 전체 리디렉션 URI에 액세스하고 액세스 토큰 (및 기타 매개 변수)을 추출 할 수있는 웹 페이지 (일반적으로 스크립트가 포함 된 HTML 문서)를 반환합니다. 조각에 포함되어 있습니다. (F) 사용자 에이전트는 웹 호스팅 클라이언트 리소스에서 제공하는 스크립트를 로컬로 실행하여 액세스 토큰을 추출합니다. (G) 사용자 에이전트는 액세스 토큰을 클라이언트에 전달합니다. 암시 적 허가 사용에 대한 배경 정보는 섹션 1.3.2 및 9를 참조하십시오. 암시 적 허용을 사용할 때 중요한 보안 고려 사항은 섹션 10.3 및 10.16을 참조하십시오.




 4.2.1. Authorization Request
 The client constructs the request URI by adding the following parameters to the query component of the authorization endpoint URI using the "application/x-www-form-urlencoded" format, per Appendix B:

 response_type
 REQUIRED. Value MUST be set to "token".

 client_id
 REQUIRED. The client identifier as described in Section 2.2.

 redirect_uri
 OPTIONAL. As described in Section 3.1.2.

 scope
 OPTIONAL. The scope of the access request as described by Section 3.3.

 state
 RECOMMENDED. An opaque value used by the client to maintain state between the request and callback. The authorization server includes this value when redirecting the user-agent back to the client. The parameter SHOULD be used for preventing cross-site request forgery as described in Section 10.12.


 4.2.1. 승인 요청
  클라이언트는 부록 B에 따라 "application / x-www-form-urlencoded"형식을 사용하여 권한 부여 끝점 URI의 쿼리 구성 요소에 다음 매개 변수를 추가하여 요청 URI를 구성합니다.

response_type
  필수입니다. 값은 "토큰"으로 설정되어야합니다.

  client_id
  필수입니다. 섹션 2.2에 설명 된 클라이언트 식별자.

  redirect_uri
  선택 과목. 섹션 3.1.2에 설명 된대로.

  scope
  선택 과목. 섹션 3.3에 설명 된 액세스 요청의 범위.

  state
  권장합니다. 요청과 콜백 사이의 상태를 유지하기 위해 클라이언트가 사용하는 불투명 한 값입니다. 권한 부여 서버는 사용자 에이전트를 클라이언트로 다시 리디렉션 할 때이 값을 포함합니다. 매개 변수는 섹션 10.12에 설명 된대로 교차 사이트 요청 위조를 방지하기 위해 사용되어야합니다 (SHOULD).












 The client directs the resource owner to the constructed URI using an HTTP redirection response, or by other means available to it via the user-agent.
 For example, the client directs the user-agent to make the following HTTP request using TLS (with extra line breaks for display purposes only):

 GET /authorize?response_type=token&client_id=s6BhdRkqt3&state=xyz &redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb HTTP/1.1
 Host: server.example.com

 The authorization server validates the request to ensure that all required parameters are present and valid. The authorization server MUST verify that the redirection URI to which it will redirect the access token matches a redirection URI registered by the client as described in Section 3.1.2. If the request is valid, the authorization server authenticates the resource owner and obtains an authorization decision (by asking the resource owner or by establishing approval via other means). When a decision is established, the authorization server directs the user-agent to the provided client redirection URI using an HTTP redirection response, or by other means available to it via the user-agent.



클라이언트는 HTTP 리디렉션 응답을 사용하거나 사용자 에이전트를 통해 사용할 수있는 다른 방법을 사용하여 리소스 소유자를 구성된 URI로 보냅니다.
 예를 들어 클라이언트는 TLS를 사용하여 다음 HTTP 요청을 수행하도록 사용자 에이전트에 지시합니다 (표시 목적으로 만 추가 줄 바꿈 포함).

 GET /authorize?response_type=token&client_id=s6BhdRkqt3&state=xyz &redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb HTTP/1.1
 Host: server.example.com

 권한 서버는 모든 필수 매개 변수가 존재하고 유효한지 확인하기 위해 요청의 유효성을 검증합니다. 인증 서버는 액세스 토큰을 리디렉션 할 리디렉션 URI가 섹션 3.1.2에 설명 된대로 클라이언트가 등록한 리디렉션 URI와 일치하는지 확인해야합니다. 요청이 유효하면 권한 서버는 리소스 소유자를 인증하고 권한 결정을 얻습니다 (리소스 소유자에게 요청하거나 다른 방법을 통해 승인을 설정하여). 결정이 설정되면 권한 부여 서버는 HTTP 리디렉션 응답을 사용하거나 사용자 에이전트를 통해 사용할 수있는 다른 수단을 사용하여 제공된 클라이언트 리디렉션 URI로 사용자 에이전트를 보냅니다.















4.2.2. Access Token Response
If the resource owner grants the access request, the authorization server issues an access token and delivers it to the client by adding the following parameters to the fragment component of the redirection URI using the "application/x-www-form-urlencoded" format, per Appendix B:

access_token
REQUIRED. The access token issued by the authorization server.

token_type
REQUIRED. The type of the token issued as described in Section 7.1. Value is case insensitive.

expires_in
RECOMMENDED. The lifetime in seconds of the access token. For example, the value "3600" denotes that the access token will expire in one hour from the time the response was generated. If omitted, the authorization server SHOULD provide the expiration time via other means or document the default value.

scope
OPTIONAL, if identical to the scope requested by the client; otherwise, REQUIRED. The scope of the access token as described by Section 3.3.

state
REQUIRED if the "state" parameter was present in the client authorization request. The exact value received from the client.

The authorization server MUST NOT issue a refresh token. For example, the authorization server redirects the user-agent by sending the following HTTP response (with extra line breaks for display purposes only):

HTTP/1.1 302 Found
Location: http://example.com/cb#access_token=2YotnFZFEjr1zCsicMWpAA&state=xyz&token_type=example&expires_in=3600



4.2.2. 액세스 토큰 응답
리소스 소유자가 액세스 요청을 허용하면 권한 부여 서버가 액세스 토큰을 발급하고 "application / x-www-form-urlencoded"형식을 사용하여 리디렉션 URI의 조각 구성 요소에 다음 매개 변수를 추가하여 클라이언트에 전달합니다. 부록 B에 따라 :

access_token
필수입니다. 권한 부여 서버에서 발급 한 액세스 토큰입니다.

token_type
필수입니다. 7.1 절에 설명 된대로 발행 된 토큰의 유형. 값은 대소 문자를 구분하지 않습니다.

expires_in
권장합니다. 액세스 토큰의 수명 (초)입니다. 예를 들어, "3600"값은 응답이 생성 된 후 1 시간 후에 액세스 토큰이 만료됨을 나타냅니다. 생략 된 경우 권한 부여 서버는 다른 수단을 통해 만료 시간을 제공하거나 기본값을 문서화해야합니다.

scope
클라이언트가 요청한 범위와 동일한 경우 선택 사항입니다. 그렇지 않으면 필수입니다. 섹션 3.3에 설명 된 액세스 토큰의 범위.

state
클라이언트 권한 요청에 "state"매개 변수가있는 경우 필수입니다. 클라이언트로부터받은 정확한 값입니다.

권한 서버는 갱신 토큰을 발행해서는 안됩니다. 예를 들어 권한 부여 서버는 다음 HTTP 응답을 전송하여 사용자 에이전트를 리디렉션합니다 (표시 목적으로 만 추가 줄 바꿈 포함).

HTTP/1.1 302 Found
Location: http://example.com/cb#access_token=2YotnFZFEjr1zCsicMWpAA&state=xyz&token_type=example&expires_in=3600
















Developers should note that some user-agents do not support the inclusion of a fragment component in the HTTP "Location" response header field. Such clients will require using other methods for redirecting the client than a 3xx redirection response -- for example, returning an HTML page that includes a 'continue' button with an action linked to the redirection URI.


 The client MUST ignore unrecognized response parameters. The access token string size is left undefined by this specification. The client should avoid making assumptions about value sizes. The authorization server SHOULD document the size of any value it issues.


 4.2.2.1. Error Response
 If the request fails due to a missing, invalid, or mismatching redirection URI, or if the client identifier is missing or invalid, the authorization server SHOULD inform the resource owner of the error and MUST NOT automatically redirect the user-agent to the invalid redirection URI. If the resource owner denies the access request or if the request fails for reasons other than a missing or invalid redirection URI, the authorization server informs the client by adding the following parameters to the fragment component of the redirection URI using the "application/x-www-form-urlencoded" format, per Appendix B:


 개발자는 일부 사용자 에이전트가 HTTP "위치"응답 헤더 필드에 조각 구성 요소를 포함하는 것을 지원하지 않는다는 점에 유의해야합니다. 이러한 클라이언트는 3xx 리디렉션 응답 이외의 다른 방법을 사용하여 클라이언트를 리디렉션해야합니다. 예를 들어 리디렉션 URI에 연결된 작업이있는 '계속'버튼이 포함 된 HTML 페이지를 반환합니다.

 클라이언트는 인식되지 않는 응답 매개 변수를 무시해야합니다. 액세스 토큰 문자열 크기는이 사양에 정의되지 않은 상태로 남아 있습니다. 클라이언트는 값 크기에 대한 가정을 피해야합니다. 권한 부여 서버는 발행하는 값의 크기를 문서화해야합니다.


 4.2.2.1. 오류 응답
 요청이 누락, 유효하지 않거나 일치하지 않는 리디렉션 URI로 인해 실패하거나 클라이언트 식별자가 누락되거나 유효하지 않은 경우 권한 부여 서버는 리소스 소유자에게 오류를 알려야하며 사용자 에이전트를 잘못된 리디렉션으로 자동 리디렉션해서는 안됩니다. URI. 리소스 소유자가 액세스 요청을 거부하거나 누락되거나 잘못된 리디렉션 URI 이외의 이유로 요청이 실패하는 경우 권한 부여 서버는 "application / x-www-form-urlencoded"를 사용하여 리디렉션 URI의 조각 구성 요소에 다음 매개 변수를 추가하여 클라이언트에 알립니다. 부록 B에 따른  "형식 :




 error
 REQUIRED. A single ASCII [USASCII] error code from the following:

	invalid_request  
	The request is missing a required parameter, includes an  invalid parameter value, includes a parameter more than  once, or is otherwise malformed.

	unauthorized_client  
	The client is not authorized to request an access token  using this method.

	access_denied  
	The resource owner or authorization server denied the  request.

	unsupported_response_type  
	The authorization server does not support obtaining an  access token using this method.

	invalid_scope  
	The requested scope is invalid, unknown, or malformed.

	server_error  
	The authorization server encountered an unexpected  condition that prevented it from fulfilling the request.  (This error code is needed because a 500 Internal Server  Error HTTP status code cannot be returned to the client  via an HTTP redirect.)

	temporarily_unavailable  
	The authorization server is currently unable to handle  the request due to a temporary overloading or maintenance  of the server. (This error code is needed because a 503  Service Unavailable HTTP status code cannot be returned  to the client via an HTTP redirect.)

	Values for the "error" parameter MUST NOT include characters outside the set %x20-21 / %x23-5B / %x5D-7E.



 error
 필수입니다. 다음의 단일 ASCII [USASCII] 오류 코드 :

	invalid_request
	요청에 필수 매개 변수가 누락되었거나 유효하지 않은 매개 변수 값이 포함되어 있거나 매개 변수가 두 번 이상 포함되어 있거나 형식이 잘못되었습니다.

	unauthorized_client
	클라이언트는이 방법을 사용하여 액세스 토큰을 요청할 권한이 없습니다.

	access_denied
	자원 소유자 또는 권한 부여 서버가 요청을 거부했습니다.

	unsupported_response_type
	권한 부여 서버는이 방법을 사용한 액세스 토큰 획득을 지원하지 않습니다.

	invalid_scope
	요청한 범위가 잘못되었거나 알 수 없거나 형식이 잘못되었습니다.

	server_error
	권한 부여 서버가 요청을 이행하지 못하게하는 예기치 않은 조건을 발견했습니다. (이 오류 코드는 500 내부 서버 오류 HTTP 상태 코드를 HTTP 리디렉션을 통해 클라이언트로 반환 할 수 없기 때문에 필요합니다.)

	temporarily_unavailable
	권한 서버는 현재 서버의 일시적인 과부하 또는 유지 보수로 인해 요청을 처리 할 수 ​​없습니다. (이 오류 코드는 503 Service Unavailable HTTP 상태 코드를 HTTP 리디렉션을 통해 클라이언트에 반환 할 수 없기 때문에 필요합니다.)

	"오류"매개 변수의 값은 % x20-21 / % x23-5B / % x5D-7E 세트 외부의 문자를 포함하면 안됩니다 (MUST NOT).





 error_description
 OPTIONAL. Human-readable ASCII [USASCII] text providing additional information, used to assist the client developer in understanding the error that occurred. Values for the "error_description" parameter MUST NOT include characters outside the set %x20-21 / %x23-5B / %x5D-7E.

 error_uri
 OPTIONAL. A URI identifying a human-readable web page with information about the error, used to provide the client developer with additional information about the error. Values for the "error_uri" parameter MUST conform to the URI-reference syntax and thus MUST NOT include characters outside the set %x21 / %x23-5B / %x5D-7E.

 state
 REQUIRED if a "state" parameter was present in the client authorization request. The exact value received from the client.

 For example, the authorization server redirects the user-agent by sending the following HTTP response:

 HTTP/1.1 302 Found
 Location: https://client.example.com/cb#error=access_denied&state=xyz


 error_description
  선택 과목. 클라이언트 개발자가 발생한 오류를 이해하는 데 사용되는 추가 정보를 제공하는 사람이 읽을 수있는 ASCII [USASCII] 텍스트입니다. "error_description"매개 변수의 값은 % x20-21 / % x23-5B / % x5D-7E 세트 외부의 문자를 포함하면 안됩니다 (MUST NOT).

  error_uri
  선택 과목. 오류에 대한 정보가있는 사람이 읽을 수있는 웹 페이지를 식별하는 URI로, 클라이언트 개발자에게 오류에 대한 추가 정보를 제공하는 데 사용됩니다. "error_uri"매개 변수의 값은 URI 참조 구문을 준수해야하며 따라서 % x21 / % x23-5B / % x5D-7E 세트 외부의 문자를 포함하면 안됩니다.

  state
  클라이언트 인증 요청에 "state"매개 변수가있는 경우 필수입니다. 클라이언트로부터받은 정확한 값입니다.

  예를 들어 권한 부여 서버는 다음 HTTP 응답을 전송하여 사용자 에이전트를 리디렉션합니다.

  HTTP/1.1 302 Found
 Location: https://client.example.com/cb#error=access_denied&state=xyz













 4.3. Resource Owner Password Credentials Grant
 The resource owner password credentials grant type is suitable in cases where the resource owner has a trust relationship with the client, such as the device operating system or a highly privileged application. The authorization server should take special care when enabling this grant type and only allow it when other flows are not viable. This grant type is suitable for clients capable of obtaining the resource owner's credentials (username and password, typically using an interactive form). It is also used to migrate existing clients using direct authentication schemes such as HTTP Basic or Digest authentication to OAuth by converting the stored credentials to an access token.

 4.3. 리소스 소유자 암호 자격 증명 부여
  리소스 소유자 암호 자격 증명 부여 유형은 리소스 소유자가 장치 운영 체제 또는 높은 권한이있는 응용 프로그램과 같이 클라이언트와 신뢰 관계가있는 경우에 적합합니다. 권한 부여 서버는이 권한 부여 유형을 활성화 할 때 특별한주의를 기울여야하며 다른 흐름이 실행 가능하지 않은 경우에만 허용해야합니다. 이 부여 유형은 리소스 소유자의 자격 증명 (일반적으로 대화 형 양식을 사용하는 사용자 이름 및 암호)을 얻을 수있는 클라이언트에 적합합니다. 또한 저장된 자격 증명을 액세스 토큰으로 변환하여 HTTP 기본 또는 다이제스트 인증과 같은 직접 인증 체계를 사용하는 기존 클라이언트를 OAuth로 마이그레이션하는 데 사용됩니다.


     +----------+
     | Resource |
     |  Owner   |
     |          |
     +----------+
          v
          |    Resource Owner
         (A) Password Credentials
          |
          v
     +---------+                                  +---------------+
     |         |>--(B)---- Resource Owner ------->|               |
     |         |         Password Credentials     | Authorization |
     | Client  |                                  |     Server    |
     |         |<--(C)---- Access Token ---------<|               |
     |         |    (w/ Optional Refresh Token)   |               |
     +---------+                                  +---------------+

            Figure 5: Resource Owner Password Credentials Flow


 The flow illustrated in Figure 5 includes the following steps: (A) The resource owner provides the client with its username and password. (B) The client requests an access token from the authorization server's token endpoint by including the credentials received from the resource owner. When making the request, the client authenticates with the authorization server. (C) The authorization server authenticates the client and validates the resource owner credentials, and if valid, issues an access token.

그림 5에 표시된 흐름에는 다음 단계가 포함됩니다. (A) 리소스 소유자는 클라이언트에게 사용자 이름과 암호를 제공합니다. (B) 클라이언트는 리소스 소유자로부터받은 자격 증명을 포함하여 권한 부여 서버의 토큰 끝점에서 액세스 토큰을 요청합니다. 요청을 할 때 클라이언트는 권한 부여 서버로 인증합니다. (C) 권한 부여 서버는 클라이언트를 인증하고 리소스 소유자 자격 증명의 유효성을 검사하고 유효한 경우 액세스 토큰을 발급합니다.





4.3.1. Authorization Request and Response
The method through which the client obtains the resource owner credentials is beyond the scope of this specification. The client MUST discard the credentials once an access token has been obtained.

4.3.2. Access Token Request
The client makes a request to the token endpoint by adding the following parameters using the "application/x-www-form-urlencoded" format per Appendix B with a character encoding of UTF-8 in the HTTP request entity-body:

grant_type
REQUIRED. Value MUST be set to "password".

username
REQUIRED. The resource owner username.

password
REQUIRED. The resource owner password.

scope
OPTIONAL. The scope of the access request as described by Section 3.3.

If the client type is confidential or the client was issued client credentials (or assigned other authentication requirements), the client MUST authenticate with the authorization server as described in Section 3.2.1. For example, the client makes the following HTTP request using transport-layer security (with extra line breaks for display purposes only):

POST /token HTTP/1.1
Host: server.example.com
Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
Content-Type: application/x-www-form-urlencoded
grant_type=password&username=johndoe&password=A3ddj3w




4.3.1. 승인 요청 및 응답
클라이언트가 리소스 소유자 자격 증명을 얻는 방법은이 사양의 범위를 벗어납니다. 클라이언트는 액세스 토큰이 확보되면 자격 증명을 폐기해야합니다.

4.3.2. 액세스 토큰 요청
클라이언트는 HTTP 요청 엔티티 본문에 UTF-8 문자 인코딩과 함께 부록 B에 따라 "application / x-www-form-urlencoded"형식을 사용하여 다음 매개 변수를 추가하여 토큰 엔드 포인트에 요청합니다.

grant_type
필수입니다. 값은 "password"로 설정해야합니다.

username
필수입니다. 리소스 소유자 사용자 이름입니다.

password
필수입니다. 자원 소유자 비밀번호입니다.

scope
선택 과목. 섹션 3.3에 설명 된 액세스 요청의 범위.

클라이언트 유형이 기밀이거나 클라이언트가 클라이언트 자격 증명 (또는 다른 인증 요구 사항이 할당 됨)이 발급 된 경우 클라이언트는 섹션 3.2.1에 설명 된대로 인증 서버로 인증해야합니다. 예를 들어 클라이언트는 전송 계층 보안을 사용하여 다음 HTTP 요청을 수행합니다 (표시 목적으로 만 추가 줄 바꿈 포함).

POST /token HTTP/1.1
Host: server.example.com
Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
Content-Type: application/x-www-form-urlencoded
grant_type=password&username=johndoe&password=A3ddj3w





 The authorization server MUST: o require client authentication for confidential clients or for any client that was issued client credentials (or with other authentication requirements), o authenticate the client if client authentication is included, and o validate the resource owner password credentials using its existing password validation algorithm. Since this access token request utilizes the resource owner's password, the authorization server MUST protect the endpoint against brute force attacks (e.g., using rate-limitation or generating alerts).


 4.3.3. Access Token Response
 If the access token request is valid and authorized, the authorization server issues an access token and optional refresh token as described in Section 5.1. If the request failed client authentication or is invalid, the authorization server returns an error response as described in Section 5.2.

 An example successful response:

 HTTP/1.1 200 OK
 Content-Type: application/json;charset=UTF-8
 Cache-Control: no-store
 Pragma: no-cache

 {
 "access_token":"2YotnFZFEjr1zCsicMWpAA",
 "token_type":"example",
 "expires_in":3600,
 "refresh_token":"tGzv3JOkF0XG5Qx2TlKWIA",
 "example_parameter":"example_value" }





 권한 부여 서버는 다음을 수행해야합니다. o 기밀 클라이언트 또는 클라이언트 자격 증명 (또는 기타 인증 요구 사항)이 발급 된 클라이언트에 대해 클라이언트 인증을 요구하고, o 클라이언트 인증이 포함 된 경우 클라이언트를 인증하고, o 기존 암호 검증 알고리즘을 사용하여 자원 소유자 암호 자격 증명을 검증합니다. 이 액세스 토큰 요청은 리소스 소유자의 비밀번호를 사용하므로 권한 부여 서버는 무차별 대입 공격 (예 : 속도 제한 사용 또는 경고 생성)으로부터 엔드 포인트를 보호해야합니다.


 4.3.3. 액세스 토큰 응답
 액세스 토큰 요청이 유효하고 승인 된 경우 권한 부여 서버는 섹션 5.1에 설명 된대로 액세스 토큰과 선택적 새로 고침 토큰을 발급합니다. 요청이 클라이언트 인증에 실패했거나 유효하지 않은 경우 권한 부여 서버는 섹션 5.2에 설명 된대로 오류 응답을 반환합니다.

 성공적인 응답의 예 :

 HTTP/1.1 200 OK
 Content-Type: application/json;charset=UTF-8
 Cache-Control: no-store
 Pragma: no-cache

 {
 "access_token":"2YotnFZFEjr1zCsicMWpAA",
 "token_type":"example",
 "expires_in":3600,
 "refresh_token":"tGzv3JOkF0XG5Qx2TlKWIA",
 "example_parameter":"example_value" }










 4.4. Client Credentials Grant
 The client can request an access token using only its client credentials (or other supported means of authentication) when the client is requesting access to the protected resources under its control, or those of another resource owner that have been previously arranged with the authorization server (the method of which is beyond the scope of this specification).

 The client credentials grant type MUST only be used by confidential clients.



 4.4. 클라이언트 자격 증명 부여
  클라이언트는 클라이언트가 제어하에있는 보호 된 리소스에 대한 액세스를 요청할 때 또는 이전에 권한 부여 서버에 배치 된 다른 리소스 소유자의 액세스 토큰을 요청할 때 클라이언트 자격 증명 (또는 기타 지원되는 인증 수단) 만 사용하여 액세스 토큰을 요청할 수 있습니다.  ( 그 방법은이 사양의 범위를 벗어납니다).

  클라이언트 자격 증명 부여 유형은 기밀 클라이언트 만 사용해야합니다.


     +---------+                                  +---------------+
     |         |                                  |               |
     |         |>--(A)- Client Authentication --->| Authorization |
     | Client  |                                  |     Server    |
     |         |<--(B)---- Access Token ---------<|               |
     |         |                                  |               |
     +---------+                                  +---------------+

                     Figure 6: Client Credentials Flow



 The flow illustrated in Figure 6 includes the following steps: (A) The client authenticates with the authorization server and requests an access token from the token endpoint. (B) The authorization server authenticates the client, and if valid, issues an access token.


 4.4.1. Authorization Request and Response
 Since the client authentication is used as the authorization grant, no additional authorization request is needed.

 4.4.2. Access Token Request
 The client makes a request to the token endpoint by adding the following parameters using the "application/x-www-form-urlencoded" format per Appendix B with a character encoding of UTF-8 in the HTTP request entity-body:

 grant_type
 REQUIRED. Value MUST be set to "client_credentials".

 scope
 OPTIONAL. The scope of the access request as described by Section 3.3.

 The client MUST authenticate with the authorization server as described in Section 3.2.1.


그림 6에 표시된 흐름에는 다음 단계가 포함됩니다. (A) 클라이언트는 권한 부여 서버로 인증하고 토큰 끝점에서 액세스 토큰을 요청합니다. (B) 인증 서버는 클라이언트를 인증하고 유효한 경우 액세스 토큰을 발급합니다.


  4.4.1. 승인 요청 및 응답
  클라이언트 인증이 권한 부여로 사용되므로 추가 권한 요청이 필요하지 않습니다.

  4.4.2. 액세스 토큰 요청
  클라이언트는 HTTP 요청 엔티티 본문에 UTF-8 문자 인코딩과 함께 부록 B에 따라 "application / x-www-form-urlencoded"형식을 사용하여 다음 매개 변수를 추가하여 토큰 엔드 포인트에 요청합니다.

  grant_type
  필수입니다. 값은 "client_credentials"로 설정되어야합니다.

  scope
  선택 사항. 섹션 3.3에 설명 된 액세스 요청의 범위.

  클라이언트는 섹션 3.2.1에 설명 된대로 권한 부여 서버로 인증해야합니다.






 For example, the client makes the following HTTP request using transport-layer security (with extra line breaks for display purposes only):

 POST /token HTTP/1.1
 Host: server.example.com
 Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
 Content-Type: application/x-www-form-urlencoded
 grant_type=client_credentials
 The authorization server MUST authenticate the client.

 4.4.3. Access Token Response
 If the access token request is valid and authorized, the authorization server issues an access token as described in Section 5.1. A refresh token SHOULD NOT be included. If the request failed client authentication or is invalid, the authorization server returns an error response as described in Section 5.2.
 An example successful response:

 HTTP/1.1 200 OK
 Content-Type: application/json;charset=UTF-8
 Cache-Control: no-store
 Pragma: no-cache
 {
 "access_token":"2YotnFZFEjr1zCsicMWpAA",
 "token_type":"example",
 "expires_in":3600,
 "example_parameter":"example_value"
 }




 예를 들어 클라이언트는 전송 계층 보안을 사용하여 다음 HTTP 요청을 수행합니다 (표시 목적으로 만 추가 줄 바꿈 포함).

 POST /token HTTP/1.1
 Host: server.example.com
 Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
 Content-Type: application/x-www-form-urlencoded
 grant_type=client_credentials
 The authorization server MUST authenticate the client.

 4.4.3. 액세스 토큰 응답
 액세스 토큰 요청이 유효하고 승인 된 경우 권한 부여 서버는 섹션 5.1에 설명 된대로 액세스 토큰을 발급합니다. 새로 고침 토큰은 포함하지 않아야합니다. 요청이 클라이언트 인증에 실패했거나 유효하지 않은 경우 권한 부여 서버는 섹션 5.2에 설명 된대로 오류 응답을 반환합니다.
 성공적인 응답의 예 :

 HTTP/1.1 200 OK
 Content-Type: application/json;charset=UTF-8
 Cache-Control: no-store
 Pragma: no-cache
 {
 "access_token":"2YotnFZFEjr1zCsicMWpAA",
 "token_type":"example",
 "expires_in":3600,
 "example_parameter":"example_value"
 }






 4.5. Extension Grants
 The client uses an extension grant type by specifying the grant type using an absolute URI (defined by the authorization server) as the value of the "grant_type" parameter of the token endpoint, and by adding any additional parameters necessary.

 For example, to request an access token using a Security Assertion Markup Language (SAML) 2.0 assertion grant type as defined by [OAuth-SAML2], the client could make the following HTTP request using TLS (with extra line breaks for display purposes only):


 POST /token HTTP/1.1
 Host: server.example.com
 Content-Type: application/x-www-form-urlencoded grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Asaml2- bearer&assertion=PEFzc2VydGlvbiBJc3N1ZUluc3RhbnQ9IjIwMTEtMDU [...omitted for brevity...]aG5TdGF0ZW1lbnQ-PC9Bc3NlcnRpb24-


 If the access token request is valid and authorized, the authorization server issues an access token and optional refresh token as described in Section 5.1. If the request failed client authentication or is invalid, the authorization server returns an error response as described in Section 5.2.




 4.5. 확장 교부금
 클라이언트는 토큰 엔드 포인트의 "grant_type"매개 변수 값으로 절대 URI (권한 부여 서버에 의해 정의 됨)를 사용하여 부여 유형을 지정하고 필요한 추가 매개 변수를 추가하여 확장 부여 유형을 사용합니다.

 예를 들어 [OAuth-SAML2]에서 정의한 SAML (Security Assertion Markup Language) 2.0 어설 션 부여 유형을 사용하여 액세스 토큰을 요청하려면 클라이언트가 TLS를 사용하여 다음 HTTP 요청을 수행 할 수 있습니다 (표시 목적으로 만 추가 줄 바꿈 포함). :


 POST /token HTTP/1.1
 Host: server.example.com
 Content-Type: application/x-www-form-urlencoded grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Asaml2- bearer&assertion=PEFzc2VydGlvbiBJc3N1ZUluc3RhbnQ9IjIwMTEtMDU [...omitted for brevity...]aG5TdGF0ZW1lbnQ-PC9Bc3NlcnRpb24-


 액세스 토큰 요청이 유효하고 승인 된 경우 권한 부여 서버는 섹션 5.1에 설명 된대로 액세스 토큰과 선택적 새로 고침 토큰을 발급합니다. 요청이 클라이언트 인증에 실패했거나 유효하지 않은 경우 권한 부여 서버는 섹션 5.2에 설명 된대로 오류 응답을 반환합니다.











 5. Issuing an Access Token
 If the access token request is valid and authorized, the authorization server issues an access token and optional refresh token as described in Section 5.1. If the request failed client authentication or is invalid, the authorization server returns an error response as described in Section 5.2.


 5.1. Successful Response
 The authorization server issues an access token and optional refresh token, and constructs the response by adding the following parameters to the entity-body of the HTTP response with a 200 (OK) status code:

 access_token
 REQUIRED. The access token issued by the authorization server.

 token_type
 REQUIRED. The type of the token issued as described in Section 7.1. Value is case insensitive.

 expires_in
 RECOMMENDED. The lifetime in seconds of the access token. For example, the value "3600" denotes that the access token will expire in one hour from the time the response was generated. If omitted, the authorization server SHOULD provide the expiration time via other means or document the default value.



5. 액세스 토큰 발급
 액세스 토큰 요청이 유효하고 승인 된 경우 권한 부여 서버는 섹션 5.1에 설명 된대로 액세스 토큰과 선택적 새로 고침 토큰을 발급합니다. 요청이 클라이언트 인증에 실패했거나 유효하지 않은 경우 권한 부여 서버는 섹션 5.2에 설명 된대로 오류 응답을 반환합니다.


 5.1. 성공적인 응답
 권한 부여 서버는 액세스 토큰 및 선택적 새로 고침 토큰을 발급하고 200 (OK) 상태 코드를 사용하여 HTTP 응답의 엔티티 본문에 다음 매개 변수를 추가하여 응답을 구성합니다.

 access_token
 필수입니다. 권한 부여 서버에서 발급 한 액세스 토큰입니다.

 token_type
 필수입니다. 7.1 절에 설명 된대로 발행 된 토큰의 유형. 값은 대소 문자를 구분하지 않습니다.

 expires_in
 권장합니다. 액세스 토큰의 수명 (초)입니다. 예를 들어, "3600"값은 응답이 생성 된 후 1 시간 후에 액세스 토큰이 만료됨을 나타냅니다. 생략 된 경우 권한 부여 서버는 다른 수단을 통해 만료 시간을 제공하거나 기본값을 문서화해야합니다.













 refresh_token
 OPTIONAL. The refresh token, which can be used to obtain new access tokens using the same authorization grant as described in Section 6.

 scope
 OPTIONAL, if identical to the scope requested by the client; otherwise, REQUIRED. The scope of the access token as described by Section 3.3.


 The parameters are included in the entity-body of the HTTP response using the "application/json" media type as defined by [RFC4627]. The parameters are serialized into a JavaScript Object Notation (JSON) structure by adding each parameter at the highest structure level. Parameter names and string values are included as JSON strings. Numerical values are included as JSON numbers. The order of parameters does not matter and can vary. The authorization server MUST include the HTTP "Cache-Control" response header field [RFC2616] with a value of "no-store" in any response containing tokens, credentials, or other sensitive information, as well as the "Pragma" response header field [RFC2616] with a value of "no-cache".

 For example:

 HTTP/1.1 200 OK
 Content-Type: application/json;charset=UTF-8
 Cache-Control: no-store
 Pragma: no-cache

 {
 "access_token":"2YotnFZFEjr1zCsicMWpAA",
 "token_type":"example",
 "expires_in":3600,
 "refresh_token":"tGzv3JOkF0XG5Qx2TlKWIA",
 "example_parameter":"example_value"
 }



 refresh_token
 선택 과목. 섹션 6에 설명 된 것과 동일한 권한 부여를 사용하여 새 액세스 토큰을 얻는 데 사용할 수있는 새로 고침 토큰.

 scope
 클라이언트가 요청한 범위와 동일한 경우 선택 사항입니다. 그렇지 않으면 필수입니다. 섹션 3.3에 설명 된 액세스 토큰의 범위.


 매개 변수는 [RFC4627]에 정의 된대로 "application / json"미디어 유형을 사용하여 HTTP 응답의 엔티티 본문에 포함됩니다. 매개 변수는 가장 높은 구조 수준에서 각 매개 변수를 추가하여 JSON (JavaScript Object Notation) 구조로 직렬화됩니다. 매개 변수 이름과 문자열 값은 JSON 문자열로 포함됩니다. 숫자 값은 JSON 숫자로 포함됩니다. 매개 변수의 순서는 중요하지 않으며 다를 수 있습니다. 인증 서버는 "Pragma"응답 헤더 필드뿐만 아니라 토큰, 자격 증명 또는 기타 민감한 정보를 포함하는 모든 응답에 "no-store"값이있는 HTTP "Cache-Control"응답 헤더 필드 [RFC2616]를 포함해야합니다. 값이 "no-cache"인 [RFC2616]

 예를 들면 :


 HTTP/1.1 200 OK
 Content-Type: application/json;charset=UTF-8
 Cache-Control: no-store
 Pragma: no-cache

 {
 "access_token":"2YotnFZFEjr1zCsicMWpAA",
 "token_type":"example",
 "expires_in":3600,
 "refresh_token":"tGzv3JOkF0XG5Qx2TlKWIA",
 "example_parameter":"example_value"
 }




 The client MUST ignore unrecognized value names in the response. The sizes of tokens and other values received from the authorization server are left undefined. The client should avoid making assumptions about value sizes. The authorization server SHOULD document the size of any value it issues.

클라이언트는 응답에서 인식되지 않는 값 이름을 무시해야합니다. 권한 부여 서버에서 수신 한 토큰 및 기타 값의 크기는 정의되지 않은 상태로 남아 있습니다. 클라이언트는 값 크기에 대한 가정을 피해야합니다. 권한 부여 서버는 발행하는 값의 크기를 문서화해야합니다.





5.2. Error Response
The authorization server responds with an HTTP 400 (Bad Request) status code (unless specified otherwise) and includes the following parameters with the response:

error
REQUIRED. A single ASCII [USASCII] error code from the following:

	invalid_request  
	The request is missing a required parameter, includes an  unsupported parameter value (other than grant type),  repeats a parameter, includes multiple credentials,  utilizes more than one mechanism for authenticating the  client, or is otherwise malformed.

	invalid_client  
	Client authentication failed (e.g., unknown client, no  client authentication included, or unsupported  authentication method). The authorization server MAY  return an HTTP 401 (Unauthorized) status code to indicate  which HTTP authentication schemes are supported. If the  client attempted to authenticate via the "Authorization"  request header field, the authorization server MUST  respond with an HTTP 401 (Unauthorized) status code and  include the "WWW-Authenticate" response header field  matching the authentication scheme used by the client.

	invalid_grant  
	The provided authorization grant (e.g., authorization  code, resource owner credentials) or refresh token is  invalid, expired, revoked, does not match the redirection  URI used in the authorization request, or was issued to  another client.

	unauthorized_client  
	The authenticated client is not authorized to use this  authorization grant type.

	unsupported_grant_type  
	The authorization grant type is not supported by the  authorization server.

	invalid_scope  
	The requested scope is invalid, unknown, malformed, or  exceeds the scope granted by the resource owner.

	Values for the "error" parameter MUST NOT include characters outside the set %x20-21 / %x23-5B / %x5D-7E.



5.2. 오류 응답
인증 서버는 HTTP 400 (잘못된 요청) 상태 코드 (달리 지정하지 않는 한)로 응답하고 응답에 다음 매개 변수를 포함합니다.

error
필수입니다. 다음의 단일 ASCII [USASCII] 오류 코드 :

	invalid_request
	요청에 필수 매개 변수가 누락되었거나 지원되지 않는 매개 변수 값 (부여 유형 제외)이 포함되어 있거나 매개 변수를 반복하거나 여러 자격 증명을 포함하거나 클라이언트 인증을 위해 둘 이상의 메커니즘을 사용하거나 기타 형식이 잘못되었습니다.

	invalid_client
	클라이언트 인증에 실패했습니다 (예 : 알 수없는 클라이언트, 포함 된 클라이언트 인증 없음 또는 지원되지 않는 인증 방법). 인증 서버는 HTTP 401 (Unauthorized) 상태 코드를 반환하여 지원되는 HTTP 인증 체계를 나타낼 수 있습니다. 클라이언트가 "Authorization"요청 헤더 필드를 통해 인증을 시도한 경우 인증 서버는 반드시 HTTP 401 (Unauthorized) 상태 코드로 응답하고 클라이언트가 사용하는 인증 체계와 일치하는 "WWW-Authenticate"응답 헤더 필드를 포함해야합니다.

	invalid_grant
	제공된 권한 부여 (예 : 권한 부여 코드, 리소스 소유자 자격 증명) 또는 새로 고침 토큰이 유효하지 않거나 만료되었거나 취소되었거나 권한 요청에 사용 된 리디렉션 URI와 일치하지 않거나 다른 클라이언트에 발급되었습니다.

	unauthorized_client
	인증 된 클라이언트는이 권한 부여 유형을 사용할 권한이 없습니다.

	unsupported_grant_type
	권한 부여 유형이 권한 부여 서버에서 지원되지 않습니다.

	invalid_scope
	요청 된 범위가 잘못되었거나 알 수 없거나 형식이 잘못되었거나 리소스 소유자가 부여한 범위를 초과합니다.

	"오류"매개 변수의 값은 % x20-21 / % x23-5B / % x5D-7E 세트 외부의 문자를 포함하면 안됩니다 (MUST NOT).






error_description
OPTIONAL. Human-readable ASCII [USASCII] text providing additional information, used to assist the client developer in understanding the error that occurred. Values for the "error_description" parameter MUST NOT include characters outside the set %x20-21 / %x23-5B / %x5D-7E.


error_uri
OPTIONAL. A URI identifying a human-readable web page with information about the error, used to provide the client developer with additional information about the error. Values for the "error_uri" parameter MUST conform to the URI-reference syntax and thus MUST NOT include characters outside the set %x21 / %x23-5B / %x5D-7E.

The parameters are included in the entity-body of the HTTP response using the "application/json" media type as defined by [RFC4627]. The parameters are serialized into a JSON structure by adding each parameter at the highest structure level. Parameter names and string values are included as JSON strings. Numerical values are included as JSON numbers. The order of parameters does not matter and can vary.

For example:
HTTP/1.1 400 Bad Request
Content-Type: application/json;charset=UTF-8
Cache-Control: no-store
Pragma: no-cache

{
 "error":"invalid_request"
 }


error_description
선택 과목. 클라이언트 개발자가 발생한 오류를 이해하는 데 사용되는 추가 정보를 제공하는 사람이 읽을 수있는 ASCII [USASCII] 텍스트입니다. "error_description"매개 변수의 값은 % x20-21 / % x23-5B / % x5D-7E 세트 외부의 문자를 포함하면 안됩니다 (MUST NOT).


error_uri
선택 과목. 오류에 대한 정보가있는 사람이 읽을 수있는 웹 페이지를 식별하는 URI로, 클라이언트 개발자에게 오류에 대한 추가 정보를 제공하는 데 사용됩니다. "error_uri"매개 변수의 값은 URI 참조 구문을 준수해야하며 따라서 % x21 / % x23-5B / % x5D-7E 세트 외부의 문자를 포함하면 안됩니다.

매개 변수는 [RFC4627]에 정의 된대로 "application / json"미디어 유형을 사용하여 HTTP 응답의 엔티티 본문에 포함됩니다. 매개 변수는 가장 높은 구조 수준에서 각 매개 변수를 추가하여 JSON 구조로 직렬화됩니다. 매개 변수 이름과 문자열 값은 JSON 문자열로 포함됩니다. 숫자 값은 JSON 숫자로 포함됩니다. 매개 변수의 순서는 중요하지 않으며 다를 수 있습니다.

예를 들면 :
HTTP/1.1 400 Bad Request
Content-Type: application/json;charset=UTF-8
Cache-Control: no-store
Pragma: no-cache

{
 "error":"invalid_request"
 }







6. Refreshing an Access Token
If the authorization server issued a refresh token to the client, the client makes a refresh request to the token endpoint by adding the following parameters using the "application/x-www-form-urlencoded" format per Appendix B with a character encoding of UTF-8 in the HTTP request entity-body:

grant_type
REQUIRED. Value MUST be set to "refresh_token".

refresh_token
REQUIRED. The refresh token issued to the client.

scope
OPTIONAL. The scope of the access request as described by Section 3.3. The requested scope MUST NOT include any scope not originally granted by the resource owner, and if omitted is treated as equal to the scope originally granted by the resource owner.

Because refresh tokens are typically long-lasting credentials used to request additional access tokens, the refresh token is bound to the client to which it was issued. If the client type is confidential or the client was issued client credentials (or assigned other authentication requirements), the client MUST authenticate with the authorization server as described in Section 3.2.1.


6. 액세스 토큰 새로 고침
권한 부여 서버가 클라이언트에 새로 고침 토큰을 발행 한 경우 클라이언트는 UTF 문자 인코딩으로 부록 B에 따라 "application / x-www-form-urlencoded"형식을 사용하여 다음 매개 변수를 추가하여 토큰 끝점에 새로 고침을 요청합니다. -8 HTTP 요청 엔티티 본문 :

grant_type
필수입니다. 값은 "refresh_token"으로 설정되어야합니다.

refresh_token
필수입니다. 클라이언트에 발급 된 새로 고침 토큰입니다.

scope
선택 과목. 섹션 3.3에 설명 된 액세스 요청의 범위. 요청 된 범위는 자원 소유자가 원래 부여하지 않은 범위를 포함하지 않아야하며 생략 된 경우 자원 소유자가 원래 부여한 범위와 동일하게 처리됩니다.

새로 고침 토큰은 일반적으로 추가 액세스 토큰을 요청하는 데 사용되는 오래 지속되는 자격 증명이므로 새로 고침 토큰은 발급 된 클라이언트에 바인딩됩니다. 클라이언트 유형이 기밀이거나 클라이언트가 클라이언트 자격 증명 (또는 다른 인증 요구 사항이 할당 됨)이 발급 된 경우 클라이언트는 섹션 3.2.1에 설명 된대로 인증 서버로 인증해야합니다.








For example, the client makes the following HTTP request using transport-layer security (with extra line breaks for display purposes only):

POST /token HTTP/1.1
Host: server.example.com
Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&refresh_token=tGzv3JOkF0XG5Qx2TlKWIA

 The authorization server MUST: o require client authentication for confidential clients or for any client that was issued client credentials (or with other authentication requirements), o authenticate the client if client authentication is included and ensure that the refresh token was issued to the authenticated client, and o validate the refresh token. If valid and authorized, the authorization server issues an access token as described in Section 5.1. If the request failed verification or is invalid, the authorization server returns an error response as described in Section 5.2. The authorization server MAY issue a new refresh token, in which case the client MUST discard the old refresh token and replace it with the new refresh token. The authorization server MAY revoke the old refresh token after issuing a new refresh token to the client. If a new refresh token is issued, the refresh token scope MUST be identical to that of the refresh token included by the client in the request.



 예를 들어 클라이언트는 전송 계층 보안을 사용하여 다음 HTTP 요청을 수행합니다 (표시 목적으로 만 추가 줄 바꿈 포함).


POST /token HTTP/1.1
Host: server.example.com
Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&refresh_token=tGzv3JOkF0XG5Qx2TlKWIA

 권한 부여 서버는 다음을 수행해야합니다. o 기밀 클라이언트 또는 클라이언트 자격 증명 (또는 기타 인증 요구 사항)이 발급 된 클라이언트에 대해 클라이언트 인증을 요구하고, o 클라이언트 인증이 포함 된 경우 클라이언트를 인증하고, 인증 된 클라이언트에 새로 고침 토큰이 발급되었는지 확인합니다. , o 새로 고침 토큰의 유효성을 검사합니다. 유효하고 권한이 부여 된 경우 권한 부여 서버는 섹션 5.1에 설명 된대로 액세스 토큰을 발급합니다. 요청이 확인에 실패했거나 유효하지 않은 경우 권한 부여 서버는 섹션 5.2에 설명 된대로 오류 응답을 반환합니다. 권한 부여 서버는 새 새로 고침 토큰을 발행 할 수 있으며,이 경우 클라이언트는 이전 새로 고침 토큰을 폐기하고 새 새로 고침 토큰으로 교체해야합니다. 권한 부여 서버는 클라이언트에 새 새로 고침 토큰을 발급 한 후 이전 새로 고침 토큰을 취소 할 수 있습니다. 새 새로 고침 토큰이 발행되면 새로 고침 토큰 범위는 클라이언트가 요청에 포함하는 새로 고침 토큰의 범위와 동일해야합니다.













 7. Accessing Protected Resources
 The client accesses protected resources by presenting the access token to the resource server. The resource server MUST validate the access token and ensure that it has not expired and that its scope covers the requested resource. The methods used by the resource server to validate the access token (as well as any error responses) are beyond the scope of this specification but generally involve an interaction or coordination between the resource server and the authorization server. The method in which the client utilizes the access token to authenticate with the resource server depends on the type of access token issued by the authorization server. Typically, it involves using the HTTP "Authorization" request header field [RFC2617] with an authentication scheme defined by the specification of the access token type used, such as [RFC6750].



7.1. Access Token Types
The access token type provides the client with the information required to successfully utilize the access token to make a protected resource request (along with type-specific attributes). The client MUST NOT use an access token if it does not understand the token type.

For example, the "bearer" token type defined in [RFC6750] is utilized by simply including the access token string in the request:

GET /resource/1 HTTP/1.1
Host: example.com
Authorization: Bearer mF_9.B5f-4.1JqM

while the "mac" token type defined in [OAuth-HTTP-MAC] is utilized by issuing a Message Authentication Code (MAC) key together with the access token that is used to sign certain components of the HTTP requests:


7. 보호 된 리소스에 액세스
 클라이언트는 리소스 서버에 액세스 토큰을 제공하여 보호 된 리소스에 액세스합니다. 리소스 서버는 액세스 토큰의 유효성을 검사하고 만료되지 않았는지 그리고 해당 범위가 요청 된 리소스를 포함하는지 확인해야합니다. 리소스 서버가 액세스 토큰 (및 오류 응답)의 유효성을 검사하는 데 사용하는 방법은이 사양의 범위를 벗어나지 만 일반적으로 리소스 서버와 권한 부여 서버 간의 상호 작용 또는 조정을 포함합니다. 클라이언트가 액세스 토큰을 사용하여 리소스 서버에 인증하는 방법은 권한 부여 서버에서 발급 한 액세스 토큰 유형에 따라 다릅니다. 일반적으로 [RFC6750]과 같이 사용 된 액세스 토큰 유형의 사양에 의해 정의 된 인증 체계와 함께 HTTP "Authorization"요청 헤더 필드 [RFC2617]를 사용합니다.


7.1. 액세스 토큰 유형
액세스 토큰 유형은 클라이언트에게 액세스 토큰을 성공적으로 활용하여 보호 된 리소스 요청 (유형별 속성과 함께)을 만드는 데 필요한 정보를 제공합니다. 클라이언트는 토큰 유형을 이해하지 못하는 경우 액세스 토큰을 사용해서는 안됩니다.

예를 들어 [RFC6750]에 정의 된 "bearer"토큰 유형은 요청에 액세스 토큰 문자열을 포함하기 만하면 활용됩니다.


GET /resource/1 HTTP/1.1
Host: example.com
Authorization: Bearer mF_9.B5f-4.1JqM

[OAuth-HTTP-MAC]에 정의 된 "mac"토큰 유형은 HTTP 요청의 특정 구성 요소에 서명하는 데 사용되는 액세스 토큰과 함께 메시지 인증 코드 (MAC) 키를 발행하여 활용됩니다.









GET /resource/1 HTTP/1.1
Host: example.com
Authorization: MAC id="h480djs93hd8",  
					nonce="274312:dj83hs9s",  
					mac="kDZvddkndxvhGRXZhvuDjEWhGeE="

The above examples are provided for illustration purposes only. Developers are advised to consult the [RFC6750] and [OAuth-HTTP-MAC] specifications before use. Each access token type definition specifies the additional attributes (if any) sent to the client together with the "access_token" response parameter. It also defines the HTTP authentication method used to include the access token when making a protected resource request.


GET /resource/1 HTTP/1.1
Host: example.com
Authorization: MAC id="h480djs93hd8",  
					nonce="274312:dj83hs9s",  
					mac="kDZvddkndxvhGRXZhvuDjEWhGeE="

위의 예는 설명 목적으로 만 제공됩니다. 개발자는 사용하기 전에 [RFC6750] 및 [OAuth-HTTP-MAC] 사양을 참조하는 것이 좋습니다. 각 액세스 토큰 유형 정의는 "access_token"응답 매개 변수와 함께 클라이언트에 전송되는 추가 속성 (있는 경우)을 지정합니다. 또한 보호 된 리소스 요청을 할 때 액세스 토큰을 포함하는 데 사용되는 HTTP 인증 방법을 정의합니다.












7.2. Error Response
If a resource access request fails, the resource server SHOULD inform the client of the error. While the specifics of such error responses are beyond the scope of this specification, this document establishes a common registry in Section 11.4 for error values to be shared among OAuth token authentication schemes. New authentication schemes designed primarily for OAuth token authentication SHOULD define a mechanism for providing an error status code to the client, in which the error values allowed are registered in the error registry established by this specification.

 Such schemes MAY limit the set of valid error codes to a subset of the registered values. If the error code is returned using a named parameter, the parameter name SHOULD be "error". Other schemes capable of being used for OAuth token authentication, but not primarily designed for that purpose, MAY bind their error values to the registry in the same manner. New authentication schemes MAY choose to also specify the use of the "error_description" and "error_uri" parameters to return error information in a manner parallel to their usage in this specification.


 8. Extensibility
 8.1. Defining Access Token Types
 Access token types can be defined in one of two ways: registered in the Access Token Types registry (following the procedures in Section 11.1), or by using a unique absolute URI as its name. Types utilizing a URI name SHOULD be limited to vendor-specific implementations that are not commonly applicable, and are specific to the implementation details of the resource server where they are used. All other types MUST be registered. Type names MUST conform to the type-name ABNF. If the type definition includes a new HTTP authentication scheme, the type name SHOULD be identical to the HTTP authentication scheme name (as defined by [RFC2617]). The token type "example" is reserved for use in examples.


 type-name = 1*name-char
 name-char = "-" / "." / "_" / DIGIT / ALPHA



7.2. 오류 응답
리소스 액세스 요청이 실패하면 리소스 서버는 클라이언트에게 오류를 알려야합니다 (SHOULD). 이러한 오류 응답의 세부 사항은이 사양의 범위를 벗어나지 만이 문서는 OAuth 토큰 인증 체계간에 오류 값을 공유하기 위해 섹션 11.4에서 공통 레지스트리를 설정합니다. 주로 OAuth 토큰 인증을 위해 설계된 새로운 인증 체계는 클라이언트에 오류 상태 코드를 제공하는 메커니즘을 정의해야합니다 (SHOULD). 여기서 허용되는 오류 값은이 사양에 의해 설정된 오류 레지스트리에 등록됩니다.

 이러한 체계는 유효한 오류 코드 세트를 등록 된 값의 하위 집합으로 제한 할 수 있습니다. 명명 된 매개 변수를 사용하여 오류 코드가 반환되는 경우 매개 변수 이름은 "오류"여야합니다. OAuth 토큰 인증에 사용할 수 있지만 주로 해당 목적으로 설계되지 않은 다른 체계는 동일한 방식으로 오류 값을 레지스트리에 바인딩 할 수 있습니다. 새로운 인증 체계는 "error_description"및 "error_uri"매개 변수의 사용을 지정하여이 사양에서의 사용과 병렬로 오류 정보를 반환하도록 선택할 수도 있습니다.


 8. 확장 성
 8.1. 액세스 토큰 유형 정의
 액세스 토큰 유형은 액세스 토큰 유형 레지스트리에 등록 (섹션 11.1의 절차에 따라)하거나 고유 한 절대 URI를 이름으로 사용하는 두 가지 방법 중 하나로 정의 할 수 있습니다. URI 이름을 사용하는 유형은 일반적으로 적용 할 수없는 공급 업체별 구현으로 제한되어야하며 이들이 사용되는 리소스 서버의 구현 세부 사항에 고유해야합니다. 다른 모든 유형은 등록해야합니다. 유형 이름은 유형 이름 ABNF를 준수해야합니다. 유형 정의에 새로운 HTTP 인증 체계가 포함 된 경우 유형 이름은 HTTP 인증 체계 이름 ([RFC2617]에 정의 된대로)과 동일해야합니다 (SHOULD). 토큰 유형 "example"은 예제에서 사용하도록 예약되어 있습니다.

 type-name = 1*name-char
 name-char = "-" / "." / "_" / DIGIT / ALPHA











 8.2. Defining New Endpoint Parameters
 New request or response parameters for use with the authorization endpoint or the token endpoint are defined and registered in the OAuth Parameters registry following the procedure in Section 11.2. Parameter names MUST conform to the param-name ABNF, and parameter values syntax MUST be well-defined (e.g., using ABNF, or a reference to the syntax of an existing parameter).

 param-name = 1*name-char
 name-char = "-" / "." / "_" / DIGIT / ALPHA


 Unregistered vendor-specific parameter extensions that are not commonly applicable and that are specific to the implementation details of the authorization server where they are used SHOULD utilize a vendor-specific prefix that is not likely to conflict with other registered values (e.g., begin with 'companyname_').


 8.3. Defining New Authorization Grant Types
 New authorization grant types can be defined by assigning them a unique absolute URI for use with the "grant_type" parameter. If the extension grant type requires additional token endpoint parameters, they MUST be registered in the OAuth Parameters registry as described by Section 11.2.8.4. Defining New Authorization Endpoint Response Types New response types for use with the authorization endpoint are defined and registered in the Authorization Endpoint Response Types registry following the procedure in Section 11.3. Response type names MUST conform to the response-type ABNF.

 response-type = response-name *( SP response-name )
 response-name = 1*response-char
 response-char = "_" / DIGIT / ALPHA


 8.2. 새 끝점 매개 변수 정의
 권한 부여 끝점 또는 토큰 끝점과 함께 사용할 새 요청 또는 응답 매개 변수는 섹션 11.2의 절차에 따라 OAuth 매개 변수 레지스트리에 정의되고 등록됩니다. 매개 변수 이름은 매개 변수 이름 ABNF를 준수해야하며 매개 변수 값 구문은 잘 정의되어야합니다 (예 : ABNF 또는 기존 매개 변수의 구문 참조).

 param-name = 1*name-char
 name-char = "-" / "." / "_" / DIGIT / ALPHA


 일반적으로 적용 할 수없고 사용되는 권한 부여 서버의 구현 세부 사항에 특정한 등록되지 않은 공급 업체별 매개 변수 확장은 다른 등록 된 값과 충돌 할 가능성이없는 공급 업체별 접두사를 사용해야합니다 (예 : '로 시작). 회사 이름_').


 8.3. 새 권한 부여 유형 정의
 새로운 권한 부여 유형은 "grant_type"매개 변수와 함께 사용할 고유 한 절대 URI를 할당하여 정의 할 수 있습니다. 확장 부여 유형에 추가 토큰 엔드 포인트 매개 변수가 필요한 경우 섹션 11.2.8.4에 설명 된대로 OAuth 매개 변수 레지스트리에 등록해야합니다. 새 권한 부여 끝점 응답 유형 정의 권한 부여 끝점과 함께 사용할 새 응답 유형은 섹션 11.3의 절차에 따라 권한 부여 끝점 응답 유형 레지스트리에 정의되고 등록됩니다. 응답 유형 이름은 응답 유형 ABNF를 준수해야합니다.

 response-type = response-name *( SP response-name )
 response-name = 1*response-char
 response-char = "_" / DIGIT / ALPHA










 If a response type contains one or more space characters (%x20), it is compared as a space-delimited list of values in which the order of values does not matter. Only one order of values can be registered, which covers all other arrangements of the same set of values. For example, the response type "token code" is left undefined by this specification. However, an extension can define and register the "token code" response type. Once registered, the same combination cannot be registered as "code token", but both values can be used to denote the same response type.



 8.5. Defining Additional Error Codes
 In cases where protocol extensions (i.e., access token types, extension parameters, or extension grant types) require additional error codes to be used with the authorization code grant error response (Section 4.1.2.1), the implicit grant error response (Section 4.2.2.1), the token error response (Section 5.2), or the resource access error response (Section 7.2), such error codes MAY be defined.


 Extension error codes MUST be registered (following the procedures in Section 11.4) if the extension they are used in conjunction with is a registered access token type, a registered endpoint parameter, or an extension grant type. Error codes used with unregistered extensions MAY be registered. Error codes MUST conform to the error ABNF and SHOULD be prefixed by an identifying name when possible. For example, an error identifying an invalid value set to the extension parameter "example" SHOULD be named "example_invalid". error = 1*error-char error-char = %x20-21 / %x23-5B / %x5D-7E


 응답 유형에 공백 문자 (% x20)가 하나 이상 포함 된 경우 값의 순서가 중요하지 않은 공백으로 구분 된 값 목록과 비교됩니다. 동일한 값 세트의 다른 모든 배열을 포함하는 하나의 값 순서 만 등록 할 수 있습니다. 예를 들어, 응답 유형 "토큰 코드"는이 사양에 정의되지 않은 상태로 남아 있습니다. 그러나 확장은 "토큰 코드"응답 유형을 정의하고 등록 할 수 있습니다. 등록한 후에는 동일한 조합을 "코드 토큰"으로 등록 할 수 없지만 두 값을 모두 사용하여 동일한 응답 유형을 나타낼 수 있습니다.



 8.5. 추가 오류 코드 정의
 프로토콜 확장 (예 : 액세스 토큰 유형, 확장 매개 변수 또는 확장 권한 부여 유형)이 권한 부여 코드 부여 오류 응답 (4.1.2.1 절), 암시 적 권한 부여 오류 응답 (4.2.2.2 절)과 함께 사용할 추가 오류 코드를 요구하는 경우 2.1), 토큰 오류 응답 (섹션 5.2) 또는 리소스 액세스 오류 응답 (섹션 7.2), 이러한 오류 코드는 정의 될 수 있습니다.


 확장 오류 코드는 함께 사용되는 확장이 등록 된 액세스 토큰 유형, 등록 된 엔드 포인트 매개 변수 또는 확장 부여 유형 인 경우 반드시 등록되어야합니다 (섹션 11.4의 절차에 따라). 등록되지 않은 확장에 사용 된 오류 코드를 등록 할 수 있습니다. 오류 코드는 오류 ABNF를 준수해야하며 가능하면 식별 이름을 접두사로 붙여야합니다 (SHOULD). 예를 들어, 확장 매개 변수 "example"에 설정된 유효하지 않은 값을 식별하는 오류는 "example_invalid"로 이름을 지정해야합니다 (SHOULD). 오류 = 1 * 오류 문자 오류 문자 = % x20-21 / % x23-5B / % x5D-7E













 9. Native Applications
 Native applications are clients installed and executed on the device used by the resource owner (i.e., desktop application, native mobile application). Native applications require special consideration related to security, platform capabilities, and overall end-user experience. The authorization endpoint requires interaction between the client and the resource owner's user-agent. Native applications can invoke an external user-agent or embed a user-agent within the application. For example: o External user-agent - the native application can capture the response from the authorization server using a redirection URI with a scheme registered with the operating system to invoke the client as the handler, manual copy-and-paste of the credentials, running a local web server, installing a user-agent extension, or by providing a redirection URI identifying a server-hosted resource under the client's control, which in turn makes the response available to the native application. o Embedded user-agent - the native application obtains the response by directly communicating with the embedded user-agent by monitoring state changes emitted during the resource load, or accessing the user-agent's cookies storage. When choosing between an external or embedded user-agent, developers should consider the following: o An external user-agent may improve completion rate, as the resource owner may already have an active session with the authorization server, removing the need to re-authenticate. It provides a familiar end-user experience and functionality. The resource owner may also rely on user-agent features or extensions to assist with authentication (e.g., password manager, 2-factor device reader). o An embedded user-agent may offer improved usability, as it removes the need to switch context and open new windows. o An embedded user-agent poses a security challenge because resource owners are authenticating in an unidentified window without access to the visual protections found in most external user-agents. An embedded user-agent educates end-users to trust unidentified requests for authentication (making phishing attacks easier to execute). When choosing between the implicit grant type and the authorization code grant type, the following should be considered: o Native applications that use the authorization code grant type SHOULD do so without using client credentials, due to the native application's inability to keep client credentials confidential. o When using the implicit grant type flow, a refresh token is not returned, which requires repeating the authorization process once the access token expires.


 10. Security Considerations
 As a flexible and extensible framework, OAuth's security considerations depend on many factors. The following sections provide implementers with security guidelines focused on the three client profiles described in Section 2.1: web application, user-agent-based application, and native application. A comprehensive OAuth security model and analysis, as well as background for the protocol design, is provided by [OAuth-THREATMODEL].


 9. 네이티브 애플리케이션
 기본 응용 프로그램은 리소스 소유자가 사용하는 장치 (예 : 데스크톱 응용 프로그램, 기본 모바일 응용 프로그램)에 설치되고 실행되는 클라이언트입니다. 기본 애플리케이션에는 보안, 플랫폼 기능 및 전반적인 최종 사용자 경험과 관련된 특별한 고려 사항이 필요합니다. 권한 부여 끝점에는 클라이언트와 리소스 소유자의 사용자 에이전트 간의 상호 작용이 필요합니다. 기본 애플리케이션은 외부 사용자 에이전트를 호출하거나 애플리케이션 내에 사용자 에이전트를 포함 할 수 있습니다. 예 : o 외부 사용자 에이전트-네이티브 애플리케이션은 클라이언트를 핸들러로 호출하기 위해 운영 체제에 등록 된 체계와 함께 리디렉션 URI를 사용하여 인증 서버에서 응답을 캡처 할 수 있습니다. 자격 증명의 수동 복사 및 붙여 넣기, 로컬 웹 서버 실행, 사용자 에이전트 확장 설치 또는 클라이언트의 제어하에 서버 호스팅 리소스를 식별하는 리디렉션 URI를 제공하여 네이티브 애플리케이션에서 응답을 사용할 수 있도록합니다. o 임베디드 사용자 에이전트-네이티브 애플리케이션은 리소스로드 중에 발생하는 상태 변경을 모니터링하거나 사용자 에이전트의 쿠키 저장소에 액세스하여 임베디드 사용자 에이전트와 직접 통신하여 응답을 얻습니다. 외부 또는 내장 된 사용자 에이전트 중에서 선택할 때 개발자는 다음 사항을 고려해야합니다 .o 자원 소유자가 이미 권한 부여 서버와 활성 세션을 가지고있을 수 있으므로 재 인증 할 필요가 없기 때문에 외부 사용자 에이전트는 완료율을 향상시킬 수 있습니다. . 익숙한 최종 사용자 경험과 기능을 제공합니다. 리소스 소유자는 인증을 지원하기 위해 사용자 에이전트 기능 또는 확장 (예 : 암호 관리자, 2 단계 장치 판독기)에 의존 할 수도 있습니다. o 포함 된 사용자 에이전트는 컨텍스트를 전환하고 새 창을 열 필요가 없으므로 향상된 사용성을 제공 할 수 있습니다. o 포함 된 사용자 에이전트는 대부분의 외부 사용자 에이전트에서 볼 수있는 시각적 보호에 액세스하지 않고 리소스 소유자가 식별되지 않은 창에서 인증하기 때문에 보안 문제가 발생합니다. 내장 된 사용자 에이전트는 최종 사용자가 식별되지 않은 인증 요청을 신뢰하도록 교육합니다 (피싱 공격을 더 쉽게 실행할 수 있음). 암시 적 부여 유형과 권한 부여 코드 부여 유형 중에서 선택할 때 다음 사항을 고려해야합니다. o 인증 코드 부여 유형을 사용하는 고유 애플리케이션은 클라이언트 인증 정보를 기밀로 유지할 수 없기 때문에 클라이언트 인증 정보를 사용하지 않고이를 수행해야합니다 (SHOULD). o 암시 적 권한 부여 유형 흐름을 사용할 때 새로 고침 토큰이 반환되지 않으므로 액세스 토큰이 만료되면 권한 부여 프로세스를 반복해야합니다.


 10. 보안 고려 사항
 유연하고 확장 가능한 프레임 워크 인 OAuth의 보안 고려 사항은 여러 요소에 따라 달라집니다. 다음 섹션에서는 섹션 2.1에 설명 된 세 가지 클라이언트 프로필 인 웹 애플리케이션, 사용자 에이전트 기반 애플리케이션 및 기본 애플리케이션에 초점을 맞춘 보안 지침을 구현 자에게 제공합니다. [OAuth-THREATMODEL]은 포괄적 인 OAuth 보안 모델 및 분석과 프로토콜 설계의 배경을 제공합니다.

















 10.1. Client Authentication
 The authorization server establishes client credentials with web application clients for the purpose of client authentication. The authorization server is encouraged to consider stronger client authentication means than a client password. Web application clients MUST ensure confidentiality of client passwords and other client credentials.


 The authorization server MUST NOT issue client passwords or other client credentials to native application or user-agent-based application clients for the purpose of client authentication. The authorization server MAY issue a client password or other credentials for a specific installation of a native application client on a specific device. When client authentication is not possible, the authorization server SHOULD employ other means to validate the client's identity -- for example, by requiring the registration of the client redirection URI or enlisting the resource owner to confirm identity. A valid redirection URI is not sufficient to verify the client's identity when asking for resource owner authorization but can be used to prevent delivering credentials to a counterfeit client after obtaining resource owner authorization. The authorization server must consider the security implications of interacting with unauthenticated clients and take measures to limit the potential exposure of other credentials (e.g., refresh tokens) issued to such clients.



 10.2. Client Impersonation
 A malicious client can impersonate another client and obtain access to protected resources if the impersonated client fails to, or is unable to, keep its client credentials confidential. The authorization server MUST authenticate the client whenever possible. If the authorization server cannot authenticate the client due to the client's nature, the authorization server MUST require the registration of any redirection URI used for receiving authorization responses and SHOULD utilize other means to protect resource owners from such potentially malicious clients. For example, the authorization server can engage the resource owner to assist in identifying the client and its origin. The authorization server SHOULD enforce explicit resource owner authentication and provide the resource owner with information about the client and the requested authorization scope and lifetime. It is up to the resource owner to review the information in the context of the current client and to authorize or deny the request. The authorization server SHOULD NOT process repeated authorization requests automatically (without active resource owner interaction) without authenticating the client or relying on other measures to ensure that the repeated request comes from the original client and not an impersonator.

10.1. 클라이언트 인증
 권한 부여 서버는 클라이언트 인증을 위해 웹 응용 프로그램 클라이언트에 클라이언트 자격 증명을 설정합니다. 권한 부여 서버는 클라이언트 비밀번호보다 더 강력한 클라이언트 인증 수단을 고려하는 것이 좋습니다. 웹 애플리케이션 클라이언트는 클라이언트 암호 및 기타 클라이언트 자격 증명의 기밀성을 보장해야합니다.


 권한 부여 서버는 클라이언트 인증을 위해 클라이언트 암호 또는 기타 클라이언트 자격 증명을 기본 응용 프로그램 또는 사용자 에이전트 기반 응용 프로그램 클라이언트에 발급해서는 안됩니다. 권한 부여 서버는 특정 장치에 네이티브 애플리케이션 클라이언트의 특정 설치를 위해 클라이언트 암호 또는 기타 자격 증명을 발급 할 수 있습니다. 클라이언트 인증이 불가능한 경우 권한 부여 서버는 클라이언트의 ID를 확인하기 위해 다른 수단을 사용해야합니다. 예를 들어 클라이언트 리디렉션 URI의 등록을 요구하거나 ID를 확인하기 위해 리소스 소유자를 등록해야합니다. 유효한 리디렉션 URI는 리소스 소유자 권한 부여를 요청할 때 클라이언트의 ID를 확인하는 데 충분하지 않지만 리소스 소유자 권한을 얻은 후 위조 클라이언트에 자격 증명을 전달하는 것을 방지하는 데 사용할 수 있습니다. 권한 부여 서버는 인증되지 않은 클라이언트와 상호 작용할 때의 보안 의미를 고려하고 해당 클라이언트에 발급 된 다른 자격 증명 (예 : 새로 고침 토큰)의 잠재적 인 노출을 제한하는 조치를 취해야합니다.



 10.2. 클라이언트 가장
 악의적 인 클라이언트는 가장 된 클라이언트가 클라이언트 자격 증명을 기밀로 유지하지 못하거나 유지할 수없는 경우 다른 클라이언트를 가장하고 보호 된 리소스에 대한 액세스 권한을 얻을 수 있습니다. 권한 서버는 가능할 때마다 클라이언트를 인증해야합니다. 권한 부여 서버가 클라이언트의 특성으로 인해 클라이언트를 인증 할 수없는 경우 권한 부여 서버는 인증 응답을 수신하는 데 사용되는 리디렉션 URI의 등록을 요구해야하며 잠재적으로 악의적 인 클라이언트로부터 리소스 소유자를 보호하기 위해 다른 수단을 사용해야합니다 (SHOULD). 예를 들어, 권한 부여 서버는 클라이언트와 해당 출처를 식별하는 데 도움을주기 위해 자원 소유자를 참여시킬 수 있습니다. 권한 부여 서버는 명시적인 리소스 소유자 인증을 시행하고 클라이언트에 대한 정보와 요청 된 권한 범위 및 수명을 리소스 소유자에게 제공해야합니다. 현재 클라이언트의 컨텍스트에서 정보를 검토하고 요청을 승인하거나 거부하는 것은 리소스 소유자에게 달려 있습니다. 권한 부여 서버는 클라이언트를 인증하거나 다른 조치에 의존하지 않고 반복 된 권한 요청을 자동으로 처리하지 않아야합니다 (반복 요청이 가장자가 아닌 원래 클라이언트에서 오는지 확인).










10.3. Access Tokens
Access token credentials (as well as any confidential access token attributes) MUST be kept confidential in transit and storage, and only shared among the authorization server, the resource servers the access token is valid for, and the client to whom the access token is issued. Access token credentials MUST only be transmitted using TLS as described in Section 1.6 with server authentication as defined by [RFC2818]. When using the implicit grant type, the access token is transmitted in the URI fragment, which can expose it to unauthorized parties. The authorization server MUST ensure that access tokens cannot be generated, modified, or guessed to produce valid access tokens by unauthorized parties. The client SHOULD request access tokens with the minimal scope necessary. The authorization server SHOULD take the client identity into account when choosing how to honor the requested scope and MAY issue an access token with less rights than requested. This specification does not provide any methods for the resource server to ensure that an access token presented to it by a given client was issued to that client by the authorization server.



10.4. Refresh Tokens
Authorization servers MAY issue refresh tokens to web application clients and native application clients. Refresh tokens MUST be kept confidential in transit and storage, and shared only among the authorization server and the client to whom the refresh tokens were issued. The authorization server MUST maintain the binding between a refresh token and the client to whom it was issued. Refresh tokens MUST only be transmitted using TLS as described in Section 1.6 with server authentication as defined by [RFC2818]. The authorization server MUST verify the binding between the refresh token and client identity whenever the client identity can be authenticated. When client authentication is not possible, the authorization server SHOULD deploy other means to detect refresh token abuse. For example, the authorization server could employ refresh token rotation in which a new refresh token is issued with every access token refresh response. The previous refresh token is invalidated but retained by the authorization server. If a refresh token is compromised and subsequently used by both the attacker and the legitimate client, one of them will present an invalidated refresh token, which will inform the authorization server of the breach. The authorization server MUST ensure that refresh tokens cannot be generated, modified, or guessed to produce valid refresh tokens by unauthorized parties.


10.3. 액세스 토큰
액세스 토큰 자격 증명 (기밀 액세스 토큰 속성 포함)은 전송 및 저장시 기밀로 유지되어야하며 권한 부여 서버, 액세스 토큰이 유효한 리소스 서버 및 액세스 토큰이 발급 된 클라이언트간에 만 공유되어야합니다. . 액세스 토큰 자격 증명은 [RFC2818]에 정의 된 서버 인증과 함께 섹션 1.6에 설명 된대로 TLS를 사용하여 전송되어야합니다. 암시 적 허용 유형을 사용하는 경우 액세스 토큰이 URI 조각으로 전송되어 권한이없는 당사자에게 노출 될 수 있습니다. 인증 서버는 인증되지 않은 당사자가 유효한 액세스 토큰을 생성하기 위해 액세스 토큰을 생성, 수정 또는 추측 할 수 없도록해야합니다. 클라이언트는 필요한 최소한의 범위로 액세스 토큰을 요청해야합니다. 인증 서버는 요청 된 범위를 존중하는 방법을 선택할 때 클라이언트 ID를 고려해야하며 요청 된 것보다 적은 권한으로 액세스 토큰을 발행 할 수 있습니다. 이 사양은 주어진 클라이언트가 제공 한 액세스 토큰이 권한 부여 서버에 의해 해당 클라이언트에 발급되었는지 확인하기 위해 리소스 서버에 대한 방법을 제공하지 않습니다.



10.4. 새로 고침 토큰
인증 서버는 웹 애플리케이션 클라이언트 및 기본 애플리케이션 클라이언트에 새로 고침 토큰을 발행 할 수 있습니다. 갱신 토큰은 전송 및 저장시 기밀로 유지되어야하며 갱신 토큰이 발행 된 인증 서버와 클라이언트간에 만 공유되어야합니다. 권한 서버는 갱신 토큰과 발행 된 클라이언트 간의 바인딩을 유지해야합니다. 새로 고침 토큰은 [RFC2818]에 정의 된 서버 인증과 함께 섹션 1.6에 설명 된대로 TLS를 사용하여 전송되어야합니다. 권한 서버는 클라이언트 ID를 인증 할 수있을 때마다 새로 고침 토큰과 클라이언트 ID 간의 바인딩을 확인해야합니다. 클라이언트 인증이 불가능한 경우 권한 부여 서버는 새로 고침 토큰 남용을 감지하기 위해 다른 수단을 배포해야합니다 (SHOULD). 예를 들어 권한 부여 서버는 모든 액세스 토큰 새로 고침 응답과 함께 새 새로 고침 토큰이 발급되는 새로 고침 토큰 순환을 사용할 수 있습니다. 이전 새로 고침 토큰은 무효화되지만 권한 부여 서버에 의해 유지됩니다. 새로 고침 토큰이 손상되어 공격자와 합법적 인 클라이언트 모두가 나중에 사용하는 경우 이들 중 하나는 무효화 된 새로 고침 토큰을 제공하여 인증 서버에 위반 사실을 알립니다. 권한 부여 서버는 권한이없는 당사자가 유효한 새로 고침 토큰을 생성하기 위해 새로 고침 토큰을 생성, 수정 또는 추측 할 수 없도록해야합니다.





10.5. Authorization Codes
The transmission of authorization codes SHOULD be made over a secure channel, and the client SHOULD require the use of TLS with its redirection URI if the URI identifies a network resource. Since authorization codes are transmitted via user-agent redirections, they could potentially be disclosed through user-agent history and HTTP referrer headers. Authorization codes operate as plaintext bearer credentials, used to verify that the resource owner who granted authorization at the authorization server is the same resource owner returning to the client to complete the process. Therefore, if the client relies on the authorization code for its own resource owner authentication, the client redirection endpoint MUST require the use of TLS. Authorization codes MUST be short lived and single-use. If the authorization server observes multiple attempts to exchange an authorization code for an access token, the authorization server SHOULD attempt to revoke all access tokens already granted based on the compromised authorization code. If the client can be authenticated, the authorization servers MUST authenticate the client and ensure that the authorization code was issued to the same client.


10.6. Authorization Code Redirection URI Manipulation
When requesting authorization using the authorization code grant type, the client can specify a redirection URI via the "redirect_uri" parameter. If an attacker can manipulate the value of the redirection URI, it can cause the authorization server to redirect the resource owner user-agent to a URI under the control of the attacker with the authorization code. An attacker can create an account at a legitimate client and initiate the authorization flow. When the attacker's user-agent is sent to the authorization server to grant access, the attacker grabs the authorization URI provided by the legitimate client and replaces the client's redirection URI with a URI under the control of the attacker. The attacker then tricks the victim into following the manipulated link to authorize access to the legitimate client. Once at the authorization server, the victim is prompted with a normal, valid request on behalf of a legitimate and trusted client, and authorizes the request. The victim is then redirected to an endpoint under the control of the attacker with the authorization code. The attacker completes the authorization flow by sending the authorization code to the client using the original redirection URI provided by the client. The client exchanges the authorization code with an access token and links it to the attacker's client account, which can now gain access to the protected resources authorized by the victim (via the client). In order to prevent such an attack, the authorization server MUST ensure that the redirection URI used to obtain the authorization code is identical to the redirection URI provided when exchanging the authorization code for an access token. The authorization server MUST require public clients and SHOULD require confidential clients to register their redirection URIs. If a redirection URI is provided in the request, the authorization server MUST validate it against the registered value.




10.5. 인증 코드
인증 코드의 전송은 보안 채널을 통해 이루어져야하며, URI가 네트워크 리소스를 식별하는 경우 클라이언트는 리디렉션 URI와 함께 TLS를 사용해야합니다 (SHOULD). 인증 코드는 사용자 에이전트 리디렉션을 통해 전송되기 때문에 잠재적으로 사용자 에이전트 기록 및 HTTP 참조 헤더를 통해 공개 될 수 있습니다. 권한 부여 코드는 권한 부여 서버에서 권한을 부여한 자원 소유자가 프로세스를 완료하기 위해 클라이언트에게 반환하는 동일한 자원 소유자인지 확인하는 데 사용되는 일반 텍스트 전달자 자격 증명으로 작동합니다. 따라서 클라이언트가 자체 리소스 소유자 인증을 위해 인증 코드에 의존하는 경우 클라이언트 리디렉션 끝점은 TLS를 사용해야합니다. 인증 코드는 수명이 짧고 일회용이어야합니다. 권한 부여 서버가 액세스 토큰에 대한 권한 코드를 교환하려는 여러 번의 시도를 관찰하는 경우 권한 부여 서버는 손상된 권한 코드를 기반으로 이미 부여 된 모든 액세스 토큰을 취소하려고 시도해야합니다 (SHOULD). 클라이언트가 인증 될 수있는 경우 권한 부여 서버는 클라이언트를 인증하고 권한 코드가 동일한 클라이언트에 발행되었는지 확인해야합니다.


10.6. 인증 코드 리디렉션 URI 조작
인증 코드 부여 유형을 사용하여 인증을 요청할 때 클라이언트는 "redirect_uri"매개 변수를 통해 리디렉션 URI를 지정할 수 있습니다. 공격자가 리디렉션 URI의 값을 조작 할 수있는 경우 권한 부여 서버가 리소스 소유자 사용자 에이전트를 권한 코드를 사용하여 공격자가 제어하는 ​​URI로 리디렉션 할 수 있습니다. 공격자는 합법적 인 클라이언트에서 계정을 만들고 인증 흐름을 시작할 수 있습니다. 공격자의 사용자 에이전트가 액세스 권한을 부여하기 위해 권한 부여 서버로 보내지면 공격자는 합법적 인 클라이언트가 제공 한 권한 부여 URI를 가져 와서 클라이언트의 리디렉션 URI를 공격자가 제어하는 ​​URI로 바꿉니다. 그런 다음 공격자는 피해자가 조작 된 링크를 따라 가도록 속여 합법적 인 클라이언트에 대한 액세스 권한을 부여합니다. 권한 부여 서버에서 피해자는 합법적이고 신뢰할 수있는 클라이언트를 대신하여 정상적이고 유효한 요청을 받고 요청을 승인합니다. 그런 다음 피해자는 인증 코드를 사용하여 공격자가 제어하는 ​​엔드 포인트로 리디렉션됩니다. 공격자는 클라이언트가 제공 한 원래 리디렉션 URI를 사용하여 클라이언트에 인증 코드를 전송하여 인증 흐름을 완료합니다. 클라이언트는 인증 코드를 액세스 토큰과 교환하고이를 공격자의 클라이언트 계정에 연결하여 이제 피해자가 인증 한 보호 된 리소스에 액세스 할 수 있습니다 (클라이언트를 통해). 이러한 공격을 방지하기 위해 인증 서버는 인증 코드를 얻기 위해 사용 된 리디렉션 URI가 액세스 토큰에 대한 인증 코드를 교환 할 때 제공된 리디렉션 URI와 동일한 지 확인해야합니다. 권한 부여 서버는 반드시 공용 클라이언트를 필요로하고 기밀 클라이언트가 리디렉션 URI를 등록하도록 요구해야합니다 (SHOULD). 요청에 리디렉션 URI가 제공되면 권한 부여 서버는 등록 된 값에 대해이를 확인해야합니다.















10.7. Resource Owner Password Credentials
The resource owner password credentials grant type is often used for legacy or migration reasons. It reduces the overall risk of storing usernames and passwords by the client but does not eliminate the need to expose highly privileged credentials to the client. This grant type carries a higher risk than other grant types because it maintains the password anti-pattern this protocol seeks to avoid. The client could abuse the password, or the password could unintentionally be disclosed to an attacker (e.g., via log files or other records kept by the client). Additionally, because the resource owner does not have control over the authorization process (the resource owner's involvement ends when it hands over its credentials to the client), the client can obtain access tokens with a broader scope than desired by the resource owner. The authorization server should consider the scope and lifetime of access tokens issued via this grant type. The authorization server and client SHOULD minimize use of this grant type and utilize other grant types whenever possible.


10.8. Request Confidentiality
Access tokens, refresh tokens, resource owner passwords, and client credentials MUST NOT be transmitted in the clear. Authorization codes SHOULD NOT be transmitted in the clear. The "state" and "scope" parameters SHOULD NOT include sensitive client or resource owner information in plain text, as they can be transmitted over insecure channels or stored insecurely.

10.9. Ensuring Endpoint Authenticity
In order to prevent man-in-the-middle attacks, the authorization server MUST require the use of TLS with server authentication as defined by [RFC2818] for any request sent to the authorization and token endpoints. The client MUST validate the authorization server's TLS certificate as defined by [RFC6125] and in accordance with its requirements for server identity authentication.



10.7. 리소스 소유자 암호 자격 증명
리소스 소유자 암호 자격 증명 부여 유형은 종종 레거시 또는 마이그레이션 이유로 사용됩니다. 클라이언트가 사용자 이름과 암호를 저장하는 전반적인 위험을 줄이지 만 높은 권한이있는 자격 증명을 클라이언트에 노출 할 필요가 없습니다. 이 권한 부여 유형은이 프로토콜이 피하려는 암호 안티 패턴을 유지하기 때문에 다른 권한 부여 유형보다 위험이 높습니다. 클라이언트가 암호를 남용 할 수 있거나 암호가 의도하지 않게 공격자에게 공개 될 수 있습니다 (예 : 클라이언트가 보관하는 로그 파일 또는 기타 기록을 통해). 또한 리소스 소유자가 권한 부여 프로세스를 제어 할 수 없기 때문에 (리소스 소유자의 참여는 클라이언트에게 자격 증명을 넘겨 줄 때 종료 됨) 클라이언트는 리소스 소유자가 원하는 것보다 더 넓은 범위의 액세스 토큰을 얻을 수 있습니다. 권한 부여 서버는이 권한 부여 유형을 통해 발급 된 액세스 토큰의 범위와 수명을 고려해야합니다. 권한 부여 서버와 클라이언트는이 권한 부여 유형의 사용을 최소화하고 가능할 때마다 다른 권한 부여 유형을 활용해야합니다.


10.8. 기밀성 요청
액세스 토큰, 새로 고침 토큰, 리소스 소유자 암호 및 클라이언트 자격 증명은 일반 정보로 전송하면 안됩니다. 인증 코드는 투명하게 전송해서는 안됩니다. "state"및 "scope"매개 변수는 안전하지 않은 채널을 통해 전송되거나 안전하지 않게 저장 될 수 있으므로 민감한 클라이언트 또는 리소스 소유자 정보를 일반 텍스트로 포함해서는 안됩니다.

10.9. 엔드 포인트 신뢰성 보장
man-in-the-middle 공격을 방지하기 위해 권한 부여 서버는 권한 부여 및 토큰 끝점으로 전송되는 모든 요청에 ​​대해 [RFC2818]에 정의 된 서버 인증과 함께 TLS를 사용해야합니다. 클라이언트는 [RFC6125]에 정의 된대로 그리고 서버 ID 인증에 대한 요구 사항에 따라 권한 부여 서버의 TLS 인증서를 확인해야합니다.












10.10. Credentials-Guessing Attacks
The authorization server MUST prevent attackers from guessing access tokens, authorization codes, refresh tokens, resource owner passwords, and client credentials. The probability of an attacker guessing generated tokens (and other credentials not intended for handling by end-users) MUST be less than or equal to 2^(-128) and SHOULD be less than or equal to 2^(-160). The authorization server MUST utilize other means to protect credentials intended for end-user usage.


10.11. Phishing Attacks
Wide deployment of this and similar protocols may cause end-users to become inured to the practice of being redirected to websites where they are asked to enter their passwords. If end-users are not careful to verify the authenticity of these websites before entering their credentials, it will be possible for attackers to exploit this practice to steal resource owners' passwords. Service providers should attempt to educate end-users about the risks phishing attacks pose and should provide mechanisms that make it easy for end-users to confirm the authenticity of their sites. Client developers should consider the security implications of how they interact with the user-agent (e.g., external, embedded), and the ability of the end-user to verify the authenticity of the authorization server. To reduce the risk of phishing attacks, the authorization servers MUST require the use of TLS on every endpoint used for end-user interaction.



10.10. 자격 증명 추측 공격
인증 서버는 공격자가 액세스 토큰, 인증 코드, 새로 고침 토큰, 리소스 소유자 비밀번호 및 클라이언트 자격 증명을 추측하지 못하도록해야합니다. 공격자가 생성 된 토큰 (및 최종 사용자가 처리하도록 의도되지 않은 기타 자격 증명)을 추측 할 확률은 2 ^ (-128)보다 작거나 같아야하고 2 ^ (-160)보다 작거나 같아야합니다 (SHOULD). 권한 부여 서버는 최종 사용자 사용을위한 자격 증명을 보호하기 위해 다른 수단을 사용해야합니다.


10.11. 피싱 공격
이 프로토콜과 유사한 프로토콜을 광범위하게 배포하면 최종 사용자가 암호를 입력하라는 웹 사이트로 리디렉션되는 관행에 빠져들게 될 수 있습니다. 최종 사용자가 자격 증명을 입력하기 전에 이러한 웹 사이트의 진위를 확인하는 데주의를 기울이지 않으면 공격자가이 방법을 악용하여 리소스 소유자의 암호를 훔칠 수 있습니다. 서비스 제공 업체는 피싱 공격으로 인한 위험에 대해 최종 사용자를 교육해야하며 최종 사용자가 사이트의 진위 여부를 쉽게 확인할 수있는 메커니즘을 제공해야합니다. 클라이언트 개발자는 사용자 에이전트와 상호 작용하는 방식 (예 : 외부, 임베디드)의 보안 의미와 최종 사용자가 권한 부여 서버의 진위를 확인하는 능력을 고려해야합니다. 피싱 공격의 위험을 줄이기 위해 권한 부여 서버는 최종 사용자 상호 작용에 사용되는 모든 엔드 포인트에서 TLS를 사용해야합니다.














10.12. Cross-Site Request Forgery
Cross-site request forgery (CSRF) is an exploit in which an attacker causes the user-agent of a victim end-user to follow a malicious URI (e.g., provided to the user-agent as a misleading link, image, or redirection) to a trusting server (usually established via the presence of a valid session cookie). A CSRF attack against the client's redirection URI allows an attacker to inject its own authorization code or access token, which can result in the client using an access token associated with the attacker's protected resources rather than the victim's (e.g., save the victim's bank account information to a protected resource controlled by the attacker). The client MUST implement CSRF protection for its redirection URI. This is typically accomplished by requiring any request sent to the redirection URI endpoint to include a value that binds the request to the user-agent's authenticated state (e.g., a hash of the session cookie used to authenticate the user-agent). The client SHOULD utilize the "state" request parameter to deliver this value to the authorization server when making an authorization request. Once authorization has been obtained from the end-user, the authorization server redirects the end-user's user-agent back to the client with the required binding value contained in the "state" parameter. The binding value enables the client to verify the validity of the request by matching the binding value to the user-agent's authenticated state. The binding value used for CSRF protection MUST contain a non-guessable value (as described in Section 10.10), and the user-agent's authenticated state (e.g., session cookie, HTML5 local storage) MUST be kept in a location accessible only to the client and the user-agent (i.e., protected by same-origin policy). A CSRF attack against the authorization server's authorization endpoint can result in an attacker obtaining end-user authorization for a malicious client without involving or alerting the end-user. The authorization server MUST implement CSRF protection for its authorization endpoint and ensure that a malicious client cannot obtain authorization without the awareness and explicit consent of the resource owner.


10.13. Clickjacking
In a clickjacking attack, an attacker registers a legitimate client and then constructs a malicious site in which it loads the authorization server's authorization endpoint web page in a transparent iframe overlaid on top of a set of dummy buttons, which are carefully constructed to be placed directly under important buttons on the authorization page. When an end-user clicks a misleading visible button, the end-user is actually clicking an invisible button on the authorization page (such as an "Authorize" button). This allows an attacker to trick a resource owner into granting its client access without the end-user's knowledge. To prevent this form of attack, native applications SHOULD use external browsers instead of embedding browsers within the application when requesting end-user authorization. For most newer browsers, avoidance of iframes can be enforced by the authorization server using the (non-standard) "x-frame-options" header. This header can have two values, "deny" and "sameorigin", which will block any framing, or framing by sites with a different origin, respectively. For older browsers, JavaScript frame-busting techniques can be used but may not be effective in all browsers.



10.12. 교차 사이트 요청 위조
CSRF (Cross-Site Request Forgery)는 공격자가 피해자 최종 사용자의 사용자 에이전트가 악성 URI를 따르도록하는 악용입니다 (예 : 사용자 에이전트에 오해의 소지가있는 링크, 이미지 또는 리디렉션으로 제공됨). 신뢰할 수있는 서버 (일반적으로 유효한 세션 쿠키의 존재를 통해 설정 됨)에 전송합니다. 클라이언트의 리디렉션 URI에 대한 CSRF 공격을 통해 공격자는 자신의 인증 코드 또는 액세스 토큰을 삽입 할 수 있습니다. 이로 인해 클라이언트가 피해자가 아닌 공격자의 보호 된 리소스와 관련된 액세스 토큰을 사용할 수 있습니다 (예 : 피해자의 은행 계좌 정보 저장). 공격자가 제어하는 ​​보호 된 자원에 대한). 클라이언트는 리디렉션 URI에 대한 CSRF 보호를 구현해야합니다. 이는 일반적으로 리디렉션 URI 엔드 포인트로 전송 된 모든 요청에 ​​요청을 사용자 에이전트의 인증 된 상태 (예 : 사용자 에이전트를 인증하는 데 사용되는 세션 쿠키의 해시)에 바인딩하는 값을 포함하도록 요구하여 수행됩니다. 클라이언트는 인증 요청을 할 때이 값을 인증 서버에 전달하기 위해 "state"요청 매개 변수를 사용해야합니다. 최종 사용자로부터 인증을 받으면 인증 서버는 "state"매개 변수에 포함 된 필수 바인딩 값을 사용하여 최종 사용자의 사용자 에이전트를 클라이언트로 다시 리디렉션합니다. 바인딩 값을 사용하면 클라이언트가 바인딩 값을 사용자 에이전트의 인증 된 상태와 일치시켜 요청의 유효성을 확인할 수 있습니다. CSRF 보호에 사용되는 바인딩 값은 추정 할 수없는 값 (섹션 10.10에 설명 됨)을 포함해야하며 사용자 에이전트의 인증 상태 (예 : 세션 쿠키, HTML5 로컬 저장소)는 클라이언트 만 액세스 할 수있는 위치에 유지되어야합니다. 및 사용자 에이전트 (즉, 동일 출처 정책에 의해 보호됨). 권한 부여 서버의 권한 부여 끝점에 대한 CSRF 공격은 공격자가 최종 사용자를 포함하거나 경고하지 않고 악의적 인 클라이언트에 대한 최종 사용자 권한을 얻을 수 있습니다. 권한 부여 서버는 권한 부여 끝점에 대한 CSRF 보호를 구현해야하며 악의적 인 클라이언트가 리소스 소유자의 인식과 명시 적 동의없이 권한을 얻을 수 없도록해야합니다.


10.13. 클릭 재킹
클릭 재킹 공격에서 공격자는 합법적 인 클라이언트를 등록한 다음 직접 배치 할 수 있도록 신중하게 구성된 더미 버튼 집합 위에 오버레이 된 투명한 iframe에서 권한 부여 서버의 권한 부여 끝점 웹 페이지를로드하는 악성 사이트를 구성합니다. 인증 페이지의 중요한 버튼 아래에 있습니다. 최종 사용자가 오해의 소지가있는 보이는 버튼을 클릭하면 최종 사용자는 실제로 인증 페이지에서 보이지 않는 버튼 (예 : "승인"버튼)을 클릭하는 것입니다. 이를 통해 공격자는 최종 사용자가 모르게 리소스 소유자를 속여 클라이언트 액세스 권한을 부여 할 수 있습니다. 이러한 형태의 공격을 방지하기 위해 네이티브 애플리케이션은 최종 사용자 인증을 요청할 때 애플리케이션 내에 브라우저를 포함하는 대신 외부 브라우저를 사용해야합니다. 대부분의 최신 브라우저의 경우 인증 서버에서 (비표준) "x-frame-options"헤더를 사용하여 iframe을 피할 수 있습니다. 이 헤더에는 "deny"및 "sameorigin"이라는 두 가지 값이있을 수 있으며, 이는 각각 다른 출처를 가진 사이트의 프레임 또는 프레임을 차단합니다. 이전 브라우저의 경우 JavaScript 프레임 버스 팅 기술을 사용할 수 있지만 모든 브라우저에서 효과적이지 않을 수 있습니다.










10.14. Code Injection and Input Validation
A code injection attack occurs when an input or otherwise external variable is used by an application unsanitized and causes modification to the application logic. This may allow an attacker to gain access to the application device or its data, cause denial of service, or introduce a wide range of malicious side-effects. The authorization server and client MUST sanitize (and validate when possible) any value received -- in particular, the value of the "state" and "redirect_uri" parameters.


10.15. Open Redirectors
The authorization server, authorization endpoint, and client redirection endpoint can be improperly configured and operate as open redirectors. An open redirector is an endpoint using a parameter to automatically redirect a user-agent to the location specified by the parameter value without any validation. Open redirectors can be used in phishing attacks, or by an attacker to get end-users to visit malicious sites by using the URI authority component of a familiar and trusted destination. In addition, if the authorization server allows the client to register only part of the redirection URI, an attacker can use an open redirector operated by the client to construct a redirection URI that will pass the authorization server validation but will send the authorization code or access token to an endpoint under the control of the attacker.



10.16. Misuse of Access Token to Impersonate Resource Owner in Implicit Flow
For public clients using implicit flows, this specification does not provide any method for the client to determine what client an access token was issued to. A resource owner may willingly delegate access to a resource by granting an access token to an attacker's malicious client. This may be due to phishing or some other pretext. An attacker may also steal a token via some other mechanism. An attacker may then attempt to impersonate the resource owner by providing the access token to a legitimate public client. In the implicit flow (response_type=token), the attacker can easily switch the token in the response from the authorization server, replacing the real access token with the one previously issued to the attacker. Servers communicating with native applications that rely on being passed an access token in the back channel to identify the user of the client may be similarly compromised by an attacker creating a compromised application that can inject arbitrary stolen access tokens. Any public client that makes the assumption that only the resource owner can present it with a valid access token for the resource is vulnerable to this type of attack. This type of attack may expose information about the resource owner at the legitimate client to the attacker (malicious client). This will also allow the attacker to perform operations at the legitimate client with the same permissions as the resource owner who originally granted the access token or authorization code. Authenticating resource owners to clients is out of scope for this specification. Any specification that uses the authorization process as a form of delegated end-user authentication to the client (e.g., third-party sign-in service) MUST NOT use the implicit flow without additional security mechanisms that would enable the client to determine if the access token was issued for its use (e.g., audience- restricting the access token).


10.14. 코드 삽입 및 입력 유효성 검사
코드 삽입 공격은 입력 또는 기타 외부 변수가 응용 프로그램에 의해 사용되어 응용 프로그램 논리를 수정하는 경우 발생합니다. 이를 통해 공격자는 애플리케이션 장치 또는 해당 데이터에 대한 액세스 권한을 얻거나 서비스 거부를 유발하거나 광범위한 악의적 인 부작용을 일으킬 수 있습니다. 권한 부여 서버와 클라이언트는 수신 된 모든 값, 특히 "state"및 "redirect_uri"매개 변수의 값을 삭제해야합니다 (가능한 경우 유효성 검사).


10.15. 리디렉터 열기
권한 부여 서버, 권한 부여 끝점 및 클라이언트 리디렉션 끝 점이 잘못 구성되어 개방형 리디렉터로 작동 할 수 있습니다. 개방형 리디렉터는 매개 변수를 사용하여 유효성 검사없이 매개 변수 값에 지정된 위치로 사용자 에이전트를 자동으로 리디렉션하는 엔드 포인트입니다. 오픈 리디렉터는 피싱 공격에 사용되거나 공격자가 익숙하고 신뢰할 수있는 대상의 URI 기관 구성 요소를 사용하여 최종 사용자가 악성 사이트를 방문하도록 유도 할 수 있습니다. 또한 권한 부여 서버가 클라이언트가 리디렉션 URI의 일부만 등록하도록 허용하는 경우 공격자는 클라이언트가 운영하는 개방형 리디렉터를 사용하여 권한 부여 서버 유효성 검사를 통과하지만 권한 부여 코드 또는 액세스 권한을 공격자의 제어하에 있는 리디렉션 URI endpoint로 보낼 수 있습니다.



10.16. 암시 적 흐름에서 리소스 소유자를 가장하기위한 액세스 토큰의 오용
암시 적 흐름을 사용하는 공용 클라이언트의 경우이 사양은 클라이언트가 액세스 토큰이 발급 된 클라이언트를 확인할 수있는 방법을 제공하지 않습니다. 리소스 소유자는 공격자의 악의적 인 클라이언트에 액세스 토큰을 부여하여 리소스에 대한 액세스 권한을 기꺼이 위임 할 수 있습니다. 이것은 피싱이나 다른 구실 때문일 수 있습니다. 공격자는 다른 메커니즘을 통해 토큰을 훔칠 수도 있습니다. 그런 다음 공격자는 합법적 인 공용 클라이언트에 액세스 토큰을 제공하여 리소스 소유자를 가장하려고 할 수 있습니다. 암시 적 흐름 (response_type = token)에서 공격자는 인증 서버의 응답에서 토큰을 쉽게 전환하여 실제 액세스 토큰을 공격자에게 이전에 발급 한 토큰으로 대체 할 수 있습니다. 클라이언트 사용자를 식별하기 위해 백 채널에서 액세스 토큰을 전달받는 데 의존하는 네이티브 애플리케이션과 통신하는 서버는 임의의 도난 된 액세스 토큰을 삽입 할 수있는 손상된 애플리케이션을 만드는 공격자에 의해 유사하게 손상 될 수 있습니다. 리소스 소유자 만 리소스에 대한 유효한 액세스 토큰을 제공 할 수 있다고 가정하는 모든 공용 클라이언트는 이러한 유형의 공격에 취약합니다. 이러한 유형의 공격은 합법적 인 클라이언트의 리소스 소유자에 대한 정보를 공격자 (악성 클라이언트)에게 노출 할 수 있습니다. 또한 공격자는 원래 액세스 토큰 또는 인증 코드를 부여한 리소스 소유자와 동일한 권한으로 합법적 인 클라이언트에서 작업을 수행 할 수 있습니다. 클라이언트에 대한 리소스 소유자 인증은이 사양의 범위를 벗어납니다. 권한 부여 프로세스를 클라이언트에 위임 된 최종 사용자 인증의 형태로 사용하는 모든 사양 (예 : 타사 로그인 서비스)은 클라이언트가 액세스 권한을 결정할 수있는 추가 보안 메커니즘없이 암시 적 흐름을 사용해서는 안됩니다. 토큰이 사용을 위해 발행되었습니다 (예 : 대상-액세스 토큰 제한).





11. IANA Considerations
11.1. OAuth Access Token Types Registry
This specification establishes the OAuth Access Token Types registry. Access token types are registered with a Specification Required ([RFC5226]) after a two-week review period on the oauth-ext-review@ietf.org mailing list, on the advice of one or more Designated Experts. However, to allow for the allocation of values prior to publication, the Designated Expert(s) may approve registration once they are satisfied that such a specification will be published. Registration requests must be sent to the oauth-ext-review@ietf.org mailing list for review and comment, with an appropriate subject (e.g., "Request for access token type: example"). Within the review period, the Designated Expert(s) will either approve or deny the registration request, communicating this decision to the review list and IANA. Denials should include an explanation and, if applicable, suggestions as to how to make the request successful. IANA must only accept registry updates from the Designated Expert(s) and should direct all requests for registration to the review mailing list.

11.1.1. Registration Template

Type name:
The name requested (e.g., "example").

Additional Token Endpoint Response Parameters:
Additional response parameters returned together with the "access_token" parameter. New parameters MUST be separately registered in the OAuth Parameters registry as described by Section 11.2.

HTTP Authentication Scheme(s):
The HTTP authentication scheme name(s), if any, used to authenticate protected resource requests using access tokens of this type.

Change controller:
For Standards Track RFCs, state "IETF". For others, give the name of the responsible party. Other details (e.g., postal address, email address, home page URI) may also be included.


11. IANA 고려 사항
11.1. OAuth 액세스 토큰 유형 레지스트리
이 사양은 OAuth 액세스 토큰 유형 레지스트리를 설정합니다. 액세스 토큰 유형은 한 명 이상의 지정 전문가의 조언에 따라 oauth-ext-review@ietf.org 메일 링리스트의 2 주 검토 기간 후 필수 사양 ([RFC5226])에 등록됩니다. 그러나 게시 전에 값 할당을 허용하기 위해 지정된 전문가 (들)는 해당 사양이 게시 될 것이라는 확신이 들면 등록을 승인 할 수 있습니다. 등록 요청은 검토 및 의견을 위해 적절한 제목 (예 : "Request for access token type : example")과 함께 oauth-ext-review@ietf.org 메일 링리스트로 보내야합니다. 검토 기간 내에 지정 전문가는 등록 요청을 승인하거나 거부하여이 결정을 검토 목록 및 IANA에 전달합니다. 거부에는 설명 및 해당되는 경우 요청을 성공적으로 수행하는 방법에 대한 제안이 포함되어야합니다. IANA는 지정된 전문가의 레지스트리 업데이트 만 수락해야하며 모든 등록 요청을 검토 메일 링리스트로 보내야합니다.

11.1.1. 등록 템플릿

Type name :
요청 된 이름 (예 : "example").

Additional Token Endpoint Response Parameters :
"access_token"매개 변수와 함께 반환되는 추가 응답 매개 변수입니다. 새로운 매개 변수는 섹션 11.2에 설명 된대로 OAuth 매개 변수 레지스트리에 별도로 등록되어야합니다.

HTTP Authentication Scheme(s) :
HTTP 인증 체계 이름 (있는 경우)은이 유형의 액세스 토큰을 사용하여 보호 된 리소스 요청을 인증하는 데 사용됩니다.

Change controller:
표준 추적 RFC의 경우 "IETF"를 명시하십시오. 다른 사람의 경우 책임 당사자의 이름을 제공하십시오. 기타 세부 정보 (예 : 우편 주소, 이메일 주소, 홈페이지 URI)도 포함될 수 있습니다.






 Specification document(s):
 Reference to the document(s) that specify the parameter, preferably including a URI that can be used to retrieve a copy of the document(s). An indication of the relevant sections may also be included but is not required.


 11.2. OAuth Parameters Registry
 This specification establishes the OAuth Parameters registry. Additional parameters for inclusion in the authorization endpoint request, the authorization endpoint response, the token endpoint request, or the token endpoint response are registered with a Specification Required ([RFC5226]) after a two-week review period on the oauth-ext-review@ietf.org mailing list, on the advice of one or more Designated Experts. However, to allow for the allocation of values prior to publication, the Designated Expert(s) may approve registration once they are satisfied that such a specification will be published. Registration requests must be sent to the oauth-ext-review@ietf.org mailing list for review and comment, with an appropriate subject (e.g., "Request for parameter: example"). Within the review period, the Designated Expert(s) will either approve or deny the registration request, communicating this decision to the review list and IANA. Denials should include an explanation and, if applicable, suggestions as to how to make the request successful. IANA must only accept registry updates from the Designated Expert(s) and should direct all requests for registration to the review mailing list.


 Specification document(s) :
 매개 변수를 지정하는 문서에 대한 참조 (가급적 문서 사본 검색에 사용할 수있는 URI 포함). 관련 섹션의 표시도 포함될 수 있지만 필수는 아닙니다.


 11.2. OAuth 매개 변수 레지스트리
 이 사양은 OAuth 매개 변수 레지스트리를 설정합니다. 인증 엔드 포인트 요청, 인증 엔드 포인트 응답, 토큰 엔드 포인트 요청 또는 토큰 엔드 포인트 응답에 포함하기위한 추가 매개 변수는 oauth-ext-review에서 2 주 검토 기간 후에 사양 필요 ([RFC5226])에 등록됩니다. 한 명 이상의 지정 전문가의 조언에 따라 @ ietf.org 메일 링리스트. 그러나 게시 전에 값 할당을 허용하기 위해 지정된 전문가 (들)는 해당 사양이 게시 될 것이라는 확신이 들면 등록을 승인 할 수 있습니다. 등록 요청은 검토 및 의견을 위해 적절한 제목 (예 : "매개 변수 요청 : 예")과 함께 oauth-ext-review@ietf.org 메일 링리스트로 보내야합니다. 검토 기간 내에 지정 전문가는 등록 요청을 승인하거나 거부하여이 결정을 검토 목록 및 IANA에 전달합니다. 거부에는 설명 및 해당되는 경우 요청을 성공적으로 수행하는 방법에 대한 제안이 포함되어야합니다. IANA는 지정된 전문가의 레지스트리 업데이트 만 수락해야하며 모든 등록 요청을 검토 메일 링리스트로 보내야합니다.















 11.2.1. Registration Template
 Parameter name:
 The name requested (e.g., "example").

 Parameter usage location:
 The location(s) where parameter can be used. The possible locations are authorization request, authorization response, token request, or token response.

 Change controller:
 For Standards Track RFCs, state "IETF". For others, give the name of the responsible party. Other details (e.g., postal address, email address, home page URI) may also be included.

 Specification document(s):
 Reference to the document(s) that specify the parameter, preferably including a URI that can be used to retrieve a copy of the document(s). An indication of the relevant sections may also be included but is not required.

 11.2.2. Initial Registry Contents
 The OAuth Parameters registry's initial contents are:

 11.2.1. 등록 템플릿
  Parameter name :
  요청 된 이름 (예 : "example").

  Parameter usage location :
  매개 변수를 사용할 수있는 위치입니다. 가능한 위치는 권한 부여 요청, 권한 부여 응답, 토큰 요청 또는 토큰 응답입니다.

  Change controller :
  표준 추적 RFC의 경우 "IETF"를 명시하십시오. 다른 사람의 경우 책임 당사자의 이름을 제공하십시오. 기타 세부 정보 (예 : 우편 주소, 이메일 주소, 홈페이지 URI)도 포함될 수 있습니다.

  Specification document(s) :
  매개 변수를 지정하는 문서에 대한 참조 (가급적 문서 사본 검색에 사용할 수있는 URI 포함). 관련 섹션의 표시도 포함될 수 있지만 필수는 아닙니다.

  11.2.2. 초기 레지스트리 내용
  OAuth 매개 변수 레지스트리의 초기 내용은 다음과 같습니다.


 o Parameter name: client_id
 o Parameter usage location: authorization request, token request
 o Change controller: IETF
 o Specification document(s): RFC 6749

 o Parameter name: client_secret
 o Parameter usage location: token request
 o Change controller: IETF
 o Specification document(s): RFC 6749

 o Parameter name: response_type
 o Parameter usage location: authorization request
 o Change controller: IETF
 o Specification document(s): RFC 6749

 o Parameter name: redirect_uri
 o Parameter usage location: authorization request, token request
 o Change controller: IETF
 o Specification document(s): RFC 6749

 o Parameter name: scope
 o Parameter usage location: authorization request, authorization response, token request, token response
 o Change controller: IETF
 o Specification document(s): RFC 6749

 o Parameter name: state
 o Parameter usage location: authorization request, authorization response
 o Change controller: IETF
 o Specification document(s): RFC 6749

 o Parameter name: code
 o Parameter usage location: authorization response, token request
 o Change controller: IETF
 o Specification document(s): RFC 6749

 o Parameter name: error_description
 o Parameter usage location: authorization response, token response
 o Change controller: IETF
 o Specification document(s): RFC 6749

 o Parameter name: error_uri
 o Parameter usage location: authorization response, token response
 o Change controller: IETF
 o Specification document(s): RFC 6749

 o Parameter name: grant_type
 o Parameter usage location: token request
 o Change controller: IETF
 o Specification document(s): RFC 6749

 o Parameter name: access_token
 o Parameter usage location: authorization response, token response
 o Change controller: IETF
 o Specification document(s): RFC 6749

 o Parameter name: token_type
 o Parameter usage location: authorization response, token response
 o Change controller: IETF
 o Specification document(s): RFC 6749

 o Parameter name: expires_in
 o Parameter usage location: authorization response, token response
 o Change controller: IETF
 o Specification document(s): RFC 6749

 o Parameter name: username
 o Parameter usage location: token request
 o Change controller: IETF
 o Specification document(s): RFC 6749

 o Parameter name: password
 o Parameter usage location: token request
 o Change controller: IETF
 o Specification document(s): RFC 6749

 o Parameter name: refresh_token
 o Parameter usage location: token request, token response
 o Change controller: IETF
 o Specification document(s): RFC 6749




11.3. OAuth Authorization Endpoint Response Types Registry

This specification establishes the OAuth Authorization Endpoint Response Types registry. Additional response types for use with the authorization endpoint are registered with a Specification Required ([RFC5226]) after a two-week review period on the oauth-ext-review@ietf.org mailing list, on the advice of one or more Designated Experts. However, to allow for the allocation of values prior to publication, the Designated Expert(s) may approve registration once they are satisfied that such a specification will be published. Registration requests must be sent to the oauth-ext-review@ietf.org mailing list for review and comment, with an appropriate subject (e.g., "Request for response type: example"). Within the review period, the Designated Expert(s) will either approve or deny the registration request, communicating this decision to the review list and IANA. Denials should include an explanation and, if applicable, suggestions as to how to make the request successful. IANA must only accept registry updates from the Designated Expert(s) and should direct all requests for registration to the review mailing list.

11.3.1. Registration Template
Response type name:
The name requested (e.g., "example").

Change controller: For Standards Track RFCs, state "IETF". For others, give the name of the responsible party. Other details (e.g., postal address, email address, home page URI) may also be included.

Specification document(s):
Reference to the document(s) that specify the type, preferably including a URI that can be used to retrieve a copy of the document(s). An indication of the relevant sections may also be included but is not required.

11.3. OAuth 권한 부여 끝점 응답 유형 레지스트리

이 사양은 OAuth 권한 부여 끝점 응답 유형 레지스트리를 설정합니다. 권한 부여 엔드 포인트와 함께 사용할 추가 응답 유형은 한 명 이상의 지정 전문가의 조언에 따라 oauth-ext-review@ietf.org 메일 링 목록에서 2 주 검토 기간 후 사양 필수 ([RFC5226])에 등록됩니다. . 그러나 게시 전에 값 할당을 허용하기 위해 지정된 전문가 (들)는 해당 사양이 게시 될 것이라는 확신이 들면 등록을 승인 할 수 있습니다. 등록 요청은 검토 및 의견을 위해 적절한 제목 (예 : "Request for response type : example")과 함께 oauth-ext-review@ietf.org 메일 링리스트로 보내야합니다. 검토 기간 내에 지정 전문가는 등록 요청을 승인하거나 거부하여이 결정을 검토 목록 및 IANA에 전달합니다. 거부에는 설명 및 해당되는 경우 요청을 성공적으로 수행하는 방법에 대한 제안이 포함되어야합니다. IANA는 지정된 전문가의 레지스트리 업데이트 만 수락해야하며 모든 등록 요청을 검토 메일 링리스트로 보내야합니다.

11.3.1. 등록 템플릿
Response type name :
요청 된 이름 (예 : "example").

Change controller : 표준 추적 RFC의 경우 "IETF"를 명시하십시오. 다른 사람의 경우 책임 당사자의 이름을 제공하십시오. 기타 세부 정보 (예 : 우편 주소, 이메일 주소, 홈페이지 URI)도 포함될 수 있습니다.

Specification document(s) :
유형을 지정하는 문서에 대한 참조. 문서의 사본을 검색하는 데 사용할 수있는 URI를 포함하는 것이 좋습니다. 관련 섹션의 표시도 포함될 수 있지만 필수는 아닙니다.






11.3.2. Initial Registry Contents

The OAuth Authorization Endpoint Response Types registry's initial contents are:

11.3.2. 초기 레지스트리 내용

OAuth 권한 부여 끝점 응답 유형 레지스트리의 초기 내용은 다음과 같습니다.

o Response type name: code
o Change controller: IETF
o Specification document(s): RFC 6749

o Response type name: token
o Change controller: IETF
o Specification document(s): RFC 6749


11.4. OAuth Extensions Error Registry
This specification establishes the OAuth Extensions Error registry. Additional error codes used together with other protocol extensions (i.e., extension grant types, access token types, or extension parameters) are registered with a Specification Required ([RFC5226]) after a two-week review period on the oauth-ext-review@ietf.org mailing list, on the advice of one or more Designated Experts. However, to allow for the allocation of values prior to publication, the Designated Expert(s) may approve registration once they are satisfied that such a specification will be published. Registration requests must be sent to the oauth-ext-review@ietf.org mailing list for review and comment, with an appropriate subject (e.g., "Request for error code: example"). Within the review period, the Designated Expert(s) will either approve or deny the registration request, communicating this decision to the review list and IANA. Denials should include an explanation and, if applicable, suggestions as to how to make the request successful. IANA must only accept registry updates from the Designated Expert(s) and should direct all requests for registration to the review mailing list.

11.4. OAuth 확장 오류 레지스트리
이 사양은 OAuth 확장 오류 레지스트리를 설정합니다. 다른 프로토콜 확장 (예 : 확장 부여 유형, 액세스 토큰 유형 또는 확장 매개 변수)과 함께 사용되는 추가 오류 코드는 oauth-ext-review @에서 2 주 검토 기간 후에 사양 필수 ([RFC5226])에 등록됩니다. 한 명 이상의 지정 전문가의 조언에 따라 ietf.org 메일 링리스트. 그러나 게시 전에 값 할당을 허용하기 위해 지정된 전문가 (들)는 해당 사양이 게시 될 것이라는 확신이 들면 등록을 승인 할 수 있습니다. 등록 요청은 검토 및 의견을 위해 적절한 제목 (예 : "오류 코드 요청 : 예")과 함께 oauth-ext-review@ietf.org 메일 링리스트로 보내야합니다. 검토 기간 내에 지정 전문가는 등록 요청을 승인하거나 거부하여이 결정을 검토 목록 및 IANA에 전달합니다. 거부에는 설명 및 해당되는 경우 요청을 성공적으로 수행하는 방법에 대한 제안이 포함되어야합니다. IANA는 지정된 전문가의 레지스트리 업데이트 만 수락해야하며 모든 등록 요청을 검토 메일 링리스트로 보내야합니다.






11.4.1. Registration Template

Error name:
The name requested (e.g., "example"). Values for the error name MUST NOT include characters outside the set %x20-21 / %x23-5B / %x5D-7E.

Error usage location:
The location(s) where the error can be used. The possible locations are authorization code grant error response (Section 4.1.2.1), implicit grant error response (Section 4.2.2.1), token error response (Section 5.2), or resource access error response (Section 7.2).

Related protocol extension:
The name of the extension grant type, access token type, or extension parameter that the error code is used in conjunction with.

Change controller:
For Standards Track RFCs, state "IETF". For others, give the name of the responsible party. Other details (e.g., postal address, email address, home page URI) may also be included.

Specification document(s):
Reference to the document(s) that specify the error code, preferably including a URI that can be used to retrieve a copy of the document(s). An indication of the relevant sections may also be included but is not required.


11.4.1. 등록 템플릿

Error name :
요청 된 이름 (예 : "example"). 오류 이름의 값은 % x20-21 / % x23-5B / % x5D-7E 세트 외부의 문자를 포함하면 안됩니다 (MUST NOT).

Error usage location :
오류를 사용할 수있는 위치입니다. 가능한 위치는 권한 부여 코드 부여 오류 응답 (4.1.2.1 단원), 암시 적 부여 오류 응답 (4.2.2.1 단원), 토큰 오류 응답 (5.2 단원) 또는 리소스 액세스 오류 응답 (7.2 단원)입니다.

Related protocol extension :
오류 코드가 함께 사용되는 확장 부여 유형, 액세스 토큰 유형 또는 확장 매개 변수의 이름입니다.

Change controller :
표준 추적 RFC의 경우 "IETF"를 명시하십시오. 다른 사람의 경우 책임 당사자의 이름을 제공하십시오. 기타 세부 정보 (예 : 우편 주소, 이메일 주소, 홈페이지 URI)도 포함될 수 있습니다.

Specification document(s) :
오류 코드를 지정하는 문서에 대한 참조. 문서의 사본을 검색하는 데 사용할 수있는 URI를 포함하는 것이 좋습니다. 관련 섹션의 표시도 포함될 수 있지만 필수는 아닙니다.











12. References
12.1. Normative References

[RFC2119] Bradner, S., "Key words for use in RFCs to Indicate Requirement Levels", BCP 14, RFC 2119, March 1997.

[RFC2246] Dierks, T. and C. Allen, "The TLS Protocol Version 1.0", RFC 2246, January 1999.

[RFC2616] Fielding, R., Gettys, J., Mogul, J., Frystyk, H., Masinter, L., Leach, P., and T. Berners-Lee, "Hypertext Transfer Protocol -- HTTP/1.1", RFC 2616, June 1999.

[RFC2617] Franks, J., Hallam-Baker, P., Hostetler, J., Lawrence, S., Leach, P., Luotonen, A., and L. Stewart, "HTTP Authentication: Basic and Digest Access Authentication", RFC 2617, June 1999.

 [RFC2818] Rescorla, E., "HTTP Over TLS", RFC 2818, May 2000.

 [RFC3629] Yergeau, F., "UTF-8, a transformation format of ISO 10646", STD 63, RFC 3629, November 2003.

 [RFC3986] Berners-Lee, T., Fielding, R., and L. Masinter, "Uniform Resource Identifier (URI): Generic Syntax", STD 66, RFC 3986, January 2005.

 [RFC4627] Crockford, D., "The application/json Media Type for JavaScript Object Notation (JSON)", RFC 4627, July 2006.

 [RFC4949] Shirey, R., "Internet Security Glossary, Version 2", RFC 4949, August 2007.

 [RFC5226] Narten, T. and H. Alvestrand, "Guidelines for Writing an IANA Considerations Section in RFCs", BCP 26, RFC 5226, May 2008.

 [RFC5234] Crocker, D. and P. Overell, "Augmented BNF for Syntax Specifications: ABNF", STD 68, RFC 5234, January 2008.

 [RFC5246] Dierks, T. and E. Rescorla, "The Transport Layer Security (TLS) Protocol Version 1.2", RFC 5246, August 2008.

 [RFC6125] Saint-Andre, P. and J. Hodges, "Representation and Verification of Domain-Based Application Service Identity within Internet Public Key Infrastructure Using X.509 (PKIX) Certificates in the Context of Transport Layer Security (TLS)", RFC 6125, March 2011.

 [USASCII] American National Standards Institute, "Coded Character Set -- 7-bit American Standard Code for Information Interchange", ANSI X3.4, 1986.

 [W3C.REC-html401-19991224] Raggett, D., Le Hors, A., and I. Jacobs, "HTML 4.01 Specification", World Wide Web Consortium Recommendation REC-html401-19991224, December 1999, <http://www.w3.org/TR/1999/REC-html401-19991224>.

 [W3C.REC-xml-20081126] Bray, T., Paoli, J., Sperberg-McQueen, C., Maler, E., and F. Yergeau, "Extensible Markup Language (XML) 1.0 (Fifth Edition)", World Wide Web Consortium  Recommendation REC-xml-20081126, November 2008, <http://www.w3.org/TR/2008/REC-xml-20081126>.



12.2. Informative References

[OAuth-HTTP-MAC] Hammer-Lahav, E., Ed., "HTTP Authentication: MAC Access Authentication", Work in Progress, February 2012.

[OAuth-SAML2] Campbell, B. and C. Mortimore, "SAML 2.0 Bearer Assertion Profiles for OAuth 2.0", Work in Progress, September 2012.

[OAuth-THREATMODEL] Lodderstedt, T., Ed., McGloin, M., and P. Hunt, "OAuth 2.0 Threat Model and Security Considerations", Work in Progress, October 2012

. [OAuth-WRAP] Hardt, D., Ed., Tom, A., Eaton, B., and Y. Goland, "OAuth Web Resource Authorization Profiles", Work in Progress, January 2010.

[RFC5849] Hammer-Lahav, E., "The OAuth 1.0 Protocol", RFC 5849, April 2010.

 [RFC6750] Jones, M. and D. Hardt, "The OAuth 2.0 Authorization Framework: Bearer Token Usage", RFC 6750, October 2012.





Appendix A. Augmented Backus-Naur Form (ABNF) Syntax

This section provides Augmented Backus-Naur Form (ABNF) syntax descriptions for the elements defined in this specification using the notation of [RFC5234]. The ABNF below is defined in terms of Unicode code points [W3C.REC-xml-20081126]; these characters are typically encoded in UTF-8. Elements are presented in the order first defined. Some of the definitions that follow use the "URI-reference" definition from [RFC3986]. Some of the definitions that follow use these common definitions:

VSCHAR = %x20-7E
NQCHAR = %x21 / %x23-5B / %x5D-7E
NQSCHAR = %x20-21 / %x23-5B / %x5D-7E
UNICODECHARNOCRLF = %x09 /%x20-7E / %x80-D7FF /  %xE000-FFFD / %x10000-10FFFF

(The UNICODECHARNOCRLF definition is based upon the Char definition in Section 2.2 of [W3C.REC-xml-20081126], but omitting the Carriage Return and Linefeed characters.)


A.1. "client_id" Syntax
The "client_id" element is defined in Section 2.3.1:
client-id = *VSCHAR

A.2. "client_secret" Syntax
The "client_secret" element is defined in Section 2.3.1: client-secret = *VSCHARA.3. "response_type" Syntax The "response_type" element is defined in Sections 3.1.1 and 8.4:

response-type = response-name *( SP response-name )
response-name = 1*response-char
response-char = "_" / DIGIT / ALPHA


부록 A. ABNF (Augmented Backus-Naur Form) 구문

이 섹션에서는 [RFC5234] 표기법을 사용하여이 사양에 정의 된 요소에 대한 ABNF (Augmented Backus-Naur Form) 구문 설명을 제공합니다. 아래 ABNF는 유니 코드 코드 포인트 [W3C.REC-xml-20081126]로 정의됩니다. 이러한 문자는 일반적으로 UTF-8로 인코딩됩니다. 요소는 처음 정의 된 순서대로 표시됩니다. 다음 정의 중 일부는 [RFC3986]의 "URI-reference"정의를 사용합니다. 다음 정의 중 일부는 다음과 같은 공통 정의를 사용합니다.

VSCHAR = % x20-7E
NQCHAR = % x21 / % x23-5B / % x5D-7E
NQSCHAR = % x20-21 / % x23-5B / % x5D-7E
UNICODECHARNOCRLF = % x09 / % x20-7E / % x80-D7FF / % xE000-FFFD / % x10000-10FFFF

(UNICODECHARNOCRLF 정의는 [W3C.REC-xml-20081126]의 섹션 2.2에있는 Char 정의를 기반으로하지만 Carriage Return 및 Linefeed 문자를 생략합니다.)


A.1. "client_id"구문
"client_id"요소는 섹션 2.3.1에 정의되어 있습니다.
클라이언트 ID = * VSCHAR

A.2. "client_secret"구문
"client_secret"요소는 섹션 2.3.1에 정의되어 있습니다. client-secret = * VSCHARA.3. "response_type"구문 "response_type"요소는 섹션 3.1.1 및 8.4에 정의되어 있습니다.

response-type = response-name *( SP response-name )
response-name = 1*response-char
response-char = "_" / DIGIT / ALPHA



A.4. "scope" Syntax
The "scope" element is defined in Section 3.3:

scope = scope-token *( SP scope-token )
scope-token = 1*NQCHAR


A.5. "state" Syntax
The "state" element is defined in Sections 4.1.1, 4.1.2, 4.1.2.1, 4.2.1, 4.2.2, and 4.2.2.1:
state = 1*VSCHAR

A.6. "redirect_uri" Syntax
The "redirect_uri" element is defined in Sections 4.1.1, 4.1.3, and 4.2.1:
redirect-uri = URI-reference

A.7. "error" Syntax
The "error" element is defined in Sections 4.1.2.1, 4.2.2.1, 5.2, 7.2, and 8.5:
error  = 1*NQSCHAR

A.8. "error_description" Syntax
The "error_description" element is defined in Sections 4.1.2.1, 4.2.2.1, 5.2, and 7.2:
error-description = 1*NQSCHAR

A.9. "error_uri" Syntax
The "error_uri" element is defined in Sections 4.1.2.1, 4.2.2.1, 5.2, and 7.2:
error-uri = URI-reference



A.10. "grant_type" Syntax
The "grant_type" element is defined in Sections 4.1.3, 4.3.2, 4.4.2, 4.5, and 6:
grant-type = grant-name / URI-reference
grant-name = 1*name-char
name-char = "-" / "." / "_" / DIGIT / ALPHA

A.11. "code" Syntax
The "code" element is defined in Section 4.1.3:
code = 1*VSCHAR

A.12. "access_token" Syntax
The "access_token" element is defined in Sections 4.2.2 and 5.1:
access-token = 1*VSCHAR

A.13. "token_type" Syntax
The "token_type" element is defined in Sections 4.2.2, 5.1, and 8.1:
token-type = type-name / URI-reference
type-name = 1*name-char
name-char = "-" / "." / "_" / DIGIT / ALPHA

A.14. "expires_in" Syntax
The "expires_in" element is defined in Sections 4.2.2 and 5.1:
expires-in = 1*DIGIT

A.15. "username" Syntax
The "username" element is defined in Section 4.3.2:
username = *UNICODECHARNOCRLF

A.16. "password" Syntax
The "password" element is defined in Section 4.3.2:
password = *UNICODECHARNOCRLF



A.17. "refresh_token" Syntax
The "refresh_token" element is defined in Sections 5.1 and 6:
refresh-token = 1*VSCHAR

A.18. Endpoint Parameter Syntax
The syntax for new endpoint parameters is defined in Section 8.2:
param-name = 1*name-char
name-char = "-" / "." / "_" / DIGIT / ALPHA


Appendix B. Use of application/x-www-form-urlencoded Media Type

At the time of publication of this specification, the "application/x-www-form-urlencoded" media type was defined in Section 17.13.4 of [W3C.REC-html401-19991224] but not registered in the IANA MIME Media Types registry (<http://www.iana.org/assignments/media-types>). Furthermore, that definition is incomplete, as it does not consider non-US-ASCII characters. To address this shortcoming when generating payloads using this media type, names and values MUST be encoded using the UTF-8 character encoding scheme [RFC3629] first; the resulting octet sequence then needs to be further encoded using the escaping rules defined in [W3C.REC-html401-19991224]. When parsing data from a payload using this media type, the names and values resulting from reversing the name/value encoding consequently need to be treated as octet sequences, to be decoded using the UTF-8 character encoding scheme. For example, the value consisting of the six Unicode code points (1) U+0020 (SPACE), (2) U+0025 (PERCENT SIGN), (3) U+0026 (AMPERSAND), (4) U+002B (PLUS SIGN), (5) U+00A3 (POUND SIGN), and (6) U+20AC (EURO SIGN) would be encoded into the octet sequence below (using hexadecimal notation):

20 25 26 2B C2 A3 E2 82 AC

and then represented in the payload as:

+%25%26%2B%C2%A3%E2%82%AC

부록 B. application / x-www-form-urlencoded 미디어 유형 사용

이 사양이 게시 될 때 "application / x-www-form-urlencoded"미디어 유형은 [W3C.REC-html401-19991224]의 섹션 17.13.4에 정의되었지만 IANA MIME 미디어 유형 레지스트리에 등록되지 않았습니다. (<http://www.iana.org/assignments/media-types>). 또한 US-ASCII가 아닌 문자를 고려하지 않기 때문에이 정의는 불완전합니다. 이 미디어 유형을 사용하여 페이로드를 생성 할 때 이러한 단점을 해결하기 위해 이름과 값은 먼저 UTF-8 문자 인코딩 체계 [RFC3629]를 사용하여 인코딩해야합니다. 결과로 생성되는 옥텟 시퀀스는 [W3C.REC-html401-19991224]에 정의 된 이스케이프 규칙을 사용하여 추가로 인코딩되어야합니다. 이 미디어 유형을 사용하여 페이로드에서 데이터를 구문 분석 할 때 결과적으로 이름 / 값 인코딩을 뒤집은 결과 이름과 값을 8 진수 시퀀스로 처리해야 UTF-8 문자 인코딩 체계를 사용하여 디코딩 할 수 있습니다. 예를 들어, 6 개의 유니 코드 코드 포인트 (1) U + 0020 (SPACE), (2) U + 0025 (PERCENT SIGN), (3) U + 0026 (AMPERSAND), (4) U + 002B ( 더하기 기호), (5) U + 00A3 (POUND SIGN) 및 (6) U + 20AC (EURO SIGN)는 16 진수 표기법을 사용하여 아래의 8 진수 시퀀스로 인코딩됩니다.

20 25 26 2B C2 A3 E2 82 AC

그런 다음 페이로드에 다음과 같이 표시됩니다.

+%25%26%2B%C2%A3%E2%82%AC




Appendix C. Acknowledgements
The initial OAuth 2.0 protocol specification was edited by David Recordon, based on two previous publications: the OAuth 1.0 community specification [RFC5849], and OAuth WRAP (OAuth Web Resource Authorization Profiles) [OAuth-WRAP]. Eran Hammer then edited many of the intermediate drafts that evolved into this RFC. The Security Considerations section was drafted by Torsten Lodderstedt, Mark McGloin, Phil Hunt, Anthony Nadalin, and John Bradley. The section on use of the "application/x-www-form-urlencoded" media type was drafted by Julian Reschke. The ABNF section was drafted by Michael B. Jones. The OAuth 1.0 community specification was edited by Eran Hammer and authored by Mark Atwood, Dirk Balfanz, Darren Bounds, Richard M. Conlan, Blaine Cook, Leah Culver, Breno de Medeiros, Brian Eaton, Kellan Elliott-McCrea, Larry Halff, Eran Hammer, Ben Laurie, Chris Messina, John Panzer, Sam Quigley, David Recordon, Eran Sandler, Jonathan Sergent, Todd Sieling, Brian Slesinsky, and Andy Smith. The OAuth WRAP specification was edited by Dick Hardt and authored by Brian Eaton, Yaron Y. Goland, Dick Hardt, and Allen Tom. This specification is the work of the OAuth Working Group, which includes dozens of active and dedicated participants. In particular, the following individuals contributed ideas, feedback, and wording that shaped and formed the final specification: Michael Adams, Amanda Anganes, Andrew Arnott, Dirk Balfanz, Aiden Bell, John Bradley, Marcos Caceres, Brian Campbell, Scott Cantor, Blaine Cook, Roger Crew, Leah Culver, Bill de hOra, Andre DeMarre, Brian Eaton, Wesley Eddy, Wolter Eldering, Brian Ellin, Igor Faynberg, George Fletcher, Tim Freeman, Luca Frosini, Evan Gilbert, Yaron Y. Goland, Brent Goldman, Kristoffer Gronowski, Eran Hammer, Dick Hardt, Justin Hart, Craig Heath, Phil Hunt, Michael B. Jones, Terry Jones, John Kemp, Mark Kent, Raffi Krikorian, Chasen Le Hara, Rasmus Lerdorf, Torsten Lodderstedt, Hui-Lan Lu, Casey Lucas, Paul Madsen, Alastair Mair, Eve Maler, James Manger, Mark McGloin, Laurence Miao, William Mills, Chuck Mortimore, Anthony Nadalin, Julian Reschke, Justin Richer, Peter Saint-Andre, Nat Sakimura, Rob Sayre, Marius Scurtescu, Naitik Shah, Luke Shepard, Vlad Skvortsov, Justin Smith, Haibin Song, Niv Steingarten, Christian Stuebner, Jeremy Suriel, Paul Tarjan, Christopher Thomas, Henry S. Thompson, Allen Tom, Franklin Tse, Nick Walker, Shane Weeden, and Skylar Woodward.


 부록 C. 감사의 말
초기 OAuth 2.0 프로토콜 사양은 OAuth 1.0 커뮤니티 사양 [RFC5849] 및 OAuth WRAP (OAuth Web Resource Authorization Profiles) [OAuth-WRAP]의 두 출판물을 기반으로 David Recordon에 의해 편집되었습니다. Eran Hammer는이 RFC로 발전한 많은 중간 초안을 편집했습니다. 보안 고려 사항 섹션은 Torsten Lodderstedt, Mark McGloin, Phil Hunt, Anthony Nadalin 및 John Bradley가 초안을 작성했습니다. "application / x-www-form-urlencoded"미디어 유형 사용에 대한 섹션은 Julian Reschke가 초안을 작성했습니다. ABNF 섹션은 Michael B. Jones가 초안을 작성했습니다. OAuth 1.0 커뮤니티 사양은 Eran Hammer가 편집하고 Mark Atwood, Dirk Balfanz, Darren Bounds, Richard M. Conlan, Blaine Cook, Leah Culver, Breno de Medeiros, Brian Eaton, Kellan Elliott-McCrea, Larry Halff, Eran Hammer가 작성했습니다. , Ben Laurie, Chris Messina, John Panzer, Sam Quigley, David Recordon, Eran Sandler, Jonathan Sergent, Todd Sieling, Brian Slesinsky 및 Andy Smith. OAuth WRAP 사양은 Dick Hardt가 편집하고 Brian Eaton, Yaron Y. Goland, Dick Hardt 및 Allen Tom이 작성했습니다. 이 사양은 수십 명의 적극적이고 헌신적 인 참가자를 포함하는 OAuth 워킹 그룹의 작업입니다. 특히 Michael Adams, Amanda Anganes, Andrew Arnott, Dirk Balfanz, Aiden Bell, John Bradley, Marcos Caceres, Brian Campbell, Scott Cantor, Blaine Cook 등 최종 사양을 형성하고 형성하는 아이디어, 피드백 및 문구에 기여한 사람은 특히 다음과 같습니다. 로저 크루, 리아 컬버, 빌 드 호라, 안드레 드 마레, 브라이언 이튼, 웨슬리 에디, 월터 엘더 링, 브라이언 엘린, 이고르 파인 버그, 조지 플레처, 팀 프리먼, 루카 프로 시니, 에반 길버트, 야론 Y. 골 랜드, 브렌트 골드만, 크리스토퍼 Gronowski, Eran Hammer, Dick Hardt, Justin Hart, Craig Heath, Phil Hunt, Michael B. Jones, Terry Jones, John Kemp, Mark Kent, Raffi Krikorian, Chasen Le Hara, Rasmus Lerdorf, Torsten Lodderstedt, Hui-Lan Lu, Casey Lucas, Paul Madsen, Alastair Mair, Eve Maler, James Manger, Mark McGloin, Laurence Miao, William Mills, Chuck Mortimore, Anthony Nadalin, Julian Reschke, Justin Richer, Peter Saint-Andre, Nat Sakimura, Rob Sayre, Marius Scurtescu, Naitik 샤, 루크 셰퍼드, 블라드 스크 보르 초프, 저스틴 스 미트 h, Haibin Song, Niv Steingarten, Christian Stuebner, Jeremy Suriel, Paul Tarjan, Christopher Thomas, Henry S. Thompson, Allen Tom, Franklin Tse, Nick Walker, Shane Weeden 및 Skylar Woodward.




 This document was produced under the chairmanship of Blaine Cook, Peter Saint-Andre, Hannes Tschofenig, Barry Leiba, and Derek Atkins. The area directors included Lisa Dusseault, Peter Saint-Andre, and Stephen Farrell.


 이 문서는 Blaine Cook, Peter Saint-Andre, Hannes Tschofenig, Barry Leiba 및 Derek Atkins의 회장으로 제작되었습니다. 지역 책임자로는 Lisa Dusseault, Peter Saint-Andre 및 Stephen Farrell이 있습니다.
