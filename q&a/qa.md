1. Sau khi thay đổi địa chỉ lcManage cần add những gì( whiteListOrg, add role)?

- Check xem org admin config trong BE đã được whiteList chưa? (Khi config env để deploy LC contract phải config MANAGEMENT_ORG = org admin trong permission
  config).
- Check xem account admin config trong BE đã được add VERIFIER_ROLE chưa? nếu chưa add VERIFIER_ROLE cho account.
- Recommend: Nên config account admin trong BE giống với account config trong env khi deploy LC contract.

2. Hệ thống bây giờ đã được add thêm ngân hàng thì cấu hình, tổ chức, quản lý như thế nào, về cả phần orgBankBLC?

- Làm sao để add subOrg?
  - Sử dụng hàm addSubOrg.
  - Option 1: Nếu devops build được blockscount có thể verify contract và sử dụng hàm addSubOrg trực tiếp.
  - Option 2:
    - Access vào node.
    - Run command to execute node: `kubectl exec -it goquorum-node-validator-1-0 -nblc-quorum -- geth attach /data/quorum/geth.ipc`
    - Run command (example):
      `quorumPermission.addSubOrg("ORG1", "SUB1", "enode://239c1f044a2b03b6c4713109af036b775c5418fe4ca63b04b1ce00124af00ddab7cc088fc46020cdc783b6207efe624551be4c06a994993d8d70f684688fb7cf@127.0.0.1:21006?discport=0", { *from*: eth.accounts[0] },);`
      Following docs:
      [https://docs.goquorum.consensys.io/configure-and-manage/manage/enhanced-permissions#example-2](https://docs.goquorum.consensys.io/configure-and-manage/manage/enhanced-permissions#example-2)
- Sau khi tạo subOrg cần làm gì để public onchain?
  - Org phải được whitelist.
  - Account dùng để kí acknowledge message, approval message phải là account có role VERIFIER_ROLE.
- The breadth and depth of the sub org tree: depth = 4, breadth = 4.

```
// Role list
// Special role has authority to execute LC Protocol's operations
// - DEFAULT_ADMIN_ROLE (Admin of LC Protocol):
//     - Grant/Revoke other special roles (including Admin)
//     - Has authority to manage special settings/operations in the LC Protocol
// - VERIFIER_ROLE:
//     - Has authority to sign acknowledge message
// - OPERATOR_ROLE:
//     - Has authority to close LC contracts
```

- Làm thế nào để addRole?
  - Sử dụng hàm grantRole trong middleware.
- Làm thế nào để xem listOrg, listAccount?
  - Access vào node.
  - Run command to execute node: `kubectl exec -it goquorum-node-validator-1-0 -nblc-quorum -- geth attach /data/quorum/geth.ipc`
  - Run command (example):
    `quorumPermission.addSubOrg("ORG1", "SUB1", "enode://239c1f044a2b03b6c4713109af036b775c5418fe4ca63b04b1ce00124af00ddab7cc088fc46020cdc783b6207efe624551be4c06a994993d8d70f684688fb7cf@127.0.0.1:21006?discport=0", { *from*: eth.accounts[0] },);`
  - Use commands quorumPermission.acctList, quorumPermission.orgList

3. Các lỗi thường xảy ra.

- 'execution reverted: Already approved' → lỗi này gặp khi thực hiện updateLC, lỗi này xảy ra khi update một bước mà bước đó đã được update trước đó.
- 'execution reverted: Invalid signing time’ → signing time is invalid
- 'execution reverted: Unlink to previous’ → xảy ra khi tu chỉnh, các lỗi có thể xảy ra: approval information is unlinked to previous stage, approval stage is
  skipped, approval is linked to invalid stage/sub-stage, approval is linked to non-existed stage/sub-stage, approval is linked to stage/sub-stage that has been
  linked by another approval.
- 'execution reverted: Invalid acknowledgement’ → acknowledge signature is signed by unauthorizer, acknowledge signature is invalid due to wrong signing
  content, approval is submitted with invalid acknowledge signature - Stage 4, approval is submitted with invalid acknowledge signature - Stage 5.
- 'execution reverted: Invalid executor' → người thực hiện tu chỉnh không phải proposer.
- 'execution reverted: Invalid approver’ → when approve() is called by invalid approver.
