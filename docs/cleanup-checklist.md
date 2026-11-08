# B3-1 리소스 삭제 체크리스트

- 기준 문서: `B3-1.pdf`
- 상태: 리소스 삭제 완료, 비용 최종 확인 대기
- 목적: 실습 종료 뒤 불필요한 AWS 리소스를 정리하고 과금 위험이 없는지 확인한다.

> PDF에서 정리 완료 확인을 요구하는 리소스는 **EC2, EBS, EIP, Internet Gateway, VPC**의 최소 5종이다. 아래 체크박스는 확인된 항목만 완료 처리한다.

## 삭제 전 확인

- [x] README, 아키텍처 다이어그램, 스크린샷, 트러블슈팅 보고서가 로컬 저장소에 모두 보관되었는지 확인한다.
- [x] 웹 서버 접속 검증과 제출용 캡처가 더 이상 필요하지 않은지 확인한다.
- [x] 서울 리전(`ap-northeast-2`)을 선택했는지 확인한다.
- [x] 삭제 대상이 B3-1 실습 리소스인지 이름·태그·리소스 ID로 다시 확인한다.

## 1. EC2 인스턴스

- [x] `i-008c24655f5342f43` 인스턴스를 종료(terminate)한다.
- [x] EC2 콘솔 또는 CLI에서 인스턴스 상태가 `terminated`인지 확인한다.

<details>
<summary>EC2 종료 증거 보기</summary>

종료 전 실행 상태, 종료 요청 완료 알림, 콘솔·CLI 종료 상태를 순서대로 확인한다.

![EC2 종료 전](screenshots/cleanup-resource/01-ec2-instance-before-termination.png)

![EC2 종료 요청 완료](screenshots/cleanup-resource/02-ec2-termination-requested.png)

![EC2 terminated 상태](screenshots/cleanup-resource/03-ec2-instance-terminated.png)

![CLI terminated 상태 검증](screenshots/cleanup-resource/04-ec2-terminated-cli-verification.png)

</details>

## 2. EBS 볼륨

- [x] EC2 종료 뒤 연결된 EBS 볼륨을 조회한다.
- [ ] 루트 볼륨의 `Delete on termination` 설정에 따라 자동 삭제되었는지 확인한다.
- [ ] 남아 있는 B3-1 EBS 볼륨이 있다면 삭제한다.
- [x] EBS 볼륨 목록에 B3-1 실습용 볼륨이 남아 있지 않은지 확인한다.

<details>
<summary>EBS 잔여 볼륨 없음 증거 보기</summary>

인스턴스 종료 후 서울 리전에 EBS 볼륨이 없음을 확인했다.

![EBS 볼륨 없음](screenshots/cleanup-resource/05-ebs-volumes-empty.png)

</details>

## 3. Elastic IP(EIP)

- [x] Elastic IP가 할당되어 있는지 확인한다.
- [ ] B3-1 실습용 EIP가 있다면 인스턴스 연결을 해제한 뒤 릴리스한다.
- [x] 할당된 EIP가 없다면 `해당 없음`으로 기록한다.

<details>
<summary>Elastic IP 미할당 확인 보기</summary>

별도로 할당한 Elastic IP가 없으므로 릴리스할 EIP도 없었다.

![Elastic IP 미할당](screenshots/cleanup-resource/06-elastic-ip-not-allocated.png)

</details>

## 4. Security Group 및 키 페어

- [x] B3-1 인스턴스가 모두 종료된 뒤 `b3-1-web-sg` 보안 그룹을 삭제한다.
- [x] AWS 콘솔의 키 페어 `b3-1-ec2-key`가 더 이상 필요하지 않다면 삭제한다.
- [x] 로컬의 `b3-1-ec2-key.pem` 파일을 삭제한다.

<details>
<summary>Security Group·키 페어 삭제 증거 보기</summary>

각 리소스의 삭제 전 상태와 초록색 완료 알림을 확인한다.

![보안 그룹 삭제 전](screenshots/cleanup-resource/07-security-group-before-deletion.png)

![보안 그룹 삭제 완료](screenshots/cleanup-resource/08-security-group-deleted.png)

![키 페어 삭제 전](screenshots/cleanup-resource/09-key-pair-before-deletion.png)

![키 페어 삭제 완료](screenshots/cleanup-resource/10-key-pair-deleted.png)

![로컬 .pem 파일 삭제](screenshots/cleanup-resource/11-local-pem-deleted-masked.png)

</details>

## 5. 네트워크 리소스

### Route Table과 Public Subnet

- [x] Public Subnet과 사용자 지정 Route Table의 연결을 해제한다.
- [x] 사용자 지정 Route Table `rtb-0dbd4e6395f9d07b4`를 삭제한다.
- [x] Public Subnet `subnet-0f44599dd057adc55`를 삭제한다.

<details>
<summary>Route Table·Public Subnet 정리 증거 보기</summary>

Route Table에서 Public Subnet 연결을 해제한 뒤 Route Table과 Subnet을 순서대로 삭제했다.

![Route Table 삭제 전](screenshots/cleanup-resource/12-route-table-before-deletion.png)

![Route Table과 Subnet 연결 해제 완료](screenshots/cleanup-resource/13-route-table-subnet-disassociated.png)

![Route Table 삭제 완료](screenshots/cleanup-resource/14-route-table-deleted.png)

![Public Subnet 삭제 전](screenshots/cleanup-resource/15-public-subnet-before-deletion.png)

![Public Subnet 삭제 완료](screenshots/cleanup-resource/16-public-subnet-deleted.png)

</details>

### Internet Gateway

- [x] Internet Gateway `igw-0768c9ab5611d2f83`를 VPC에서 분리(detach)한다.
- [x] 분리한 Internet Gateway를 삭제한다.

<details>
<summary>Internet Gateway 분리·삭제 증거 보기</summary>

VPC에 연결된 IGW를 분리한 뒤 삭제했다.

![Internet Gateway 분리 전](screenshots/cleanup-resource/17-internet-gateway-before-detach.png)

![Internet Gateway 분리 완료](screenshots/cleanup-resource/18-internet-gateway-detached.png)

![Internet Gateway 삭제 완료](screenshots/cleanup-resource/19-internet-gateway-deleted.png)

</details>

### VPC

- [x] VPC에 남은 Subnet, Route Table, Security Group, IGW가 없는지 확인한다.
- [x] VPC `vpc-0d0d7e8b76a75fe5e`를 삭제한다.
- [x] VPC 목록에서 B3-1 실습용 VPC가 남아 있지 않은지 확인한다.

<details>
<summary>VPC 삭제 증거 보기</summary>

삭제 전 상태, 초록색 삭제 완료 알림, 최종 목록 검증을 순서대로 확인한다.

![VPC 삭제 전](screenshots/cleanup-resource/20-vpc-before-deletion.png)

![VPC 삭제 완료 알림](screenshots/cleanup-resource/21-vpc-deleted-notification.png)

![VPC 삭제 후 목록 검증](screenshots/cleanup-resource/22-vpc-deletion-verified.png)

</details>

## 삭제 후 최종 확인

- [x] 실행 중인 EC2 인스턴스가 남아 있지 않은지 확인한다.
- [x] EBS 볼륨과 스냅샷이 남아 있지 않은지 확인한다.
- [x] Elastic IP가 할당된 상태로 남아 있지 않은지 확인한다.
- [x] Internet Gateway와 B3-1 VPC가 남아 있지 않은지 확인한다.
- [ ] 비용 관리 화면에서 예상치 못한 사용량 또는 과금 대상 리소스가 없는지 확인한다.

## 삭제 결과 기록

삭제를 실제로 수행한 뒤에는 각 체크박스를 완료 처리하고, 필요한 경우 확인 화면 또는 CLI 결과를 추가 증거로 보관한다.
