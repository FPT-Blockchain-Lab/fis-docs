# Operating Procedures

<p align="center">
  <img width="1200" height="350" src="./images/Standard_LC_Stages.png">
</p>

Cách vận hành để thực hiện đầu đủ 1 luồng cho LC trên blockchain:

- Bước 1: Create LC (Public Onchain)
  - Nhận data từ BE, FE FIS bao gồm các thông tin:
    - @param parties involved parties in LC contract
    - @param content content of LC
    - @param from executor
  - Thực hiện check acknowledge signature (Kí lần 1).
  - Dựa vào data truyền vào generate approval signature và nội dung LC (Kí lần 2).
  - Thực hiện validate data cho việc create LC.
  - Sau khi các bước trên passed thì tiến hành create LC (public onchain).
- Bước 2: Update LC (Tương ứng với các bước 2.1, 3.1, ...)
  - Nhận data từ BE, FE FIS bao gồm các thông tin:
    - @param documentId // hash of LC number
    - @param stage
    - @param subStage
    - @param content // content of stage
    - @param from executor
  - Thực hiện check acknowledge signature (Kí lần 1).
  - Thực hiện validate thứ tự stage và sub-stage và get stage content (Tất cả các request đưa thông tin xác nhận lên on-chain cần theo sequence order).
  - Thực hiện generate prevHash.
  - Thực hiện get rootHash.
  - Thực hiện format approval content để tạo approval message hash.
  - Thực hiện get approval signature (Kí lần 2).
  - Thực hiện kiểm tra list org theo stage.
  - Sau khi các bước trên passed thì tiến hành update LC.
- Bước 3: Submit amend request (Gửi yêu cầu tu chỉnh LC lên onchain)
  - Nhận data từ BE, FE FIS bao gồm các thông tin:
    - @param documentId // hash of LC number
    - @param stage // amend stage
    - @param subStage // amend sub stage
    - @param content // amend stage content
    - @param migrateStages // an array of migrating stages
    - @param from // address of proposer
  - Thực hiện check acknowledge signature (Kí lần 1).
  - Thực hiện submit root amendment -> submit amend request.
  - Thực hiện get stage content for amend, rootHash và prevHash, content of migrate stage.
  - Thực hiện format appoval content to create approval message hash.
  - Thực hiện get approval signature (Kí lần 2).
  - Sau khi các bước trên passed thì tiến hành submit amend request onchain.
- Bước 4: Approve amend request
  - Nhận data từ BE, FE FIS bao gồm các thông tin:
    - @param documentId // hash of LC number
    - @param proposer // address of proposer
    - @param nonce
    - @param from // address of approver
  - Thực hiện get requestId (amend request).
  - Kiểm tra trạng thái của amend request.
  - Get content of amend request.
  - Format amendment request -> get amend signature (Kí lần 1).
  - Sau khi các bước trên passed thì tiến hành approve amend request.
- Bước 5: Fullfill amendment
  - Nhận data từ BE, FE FIS bao gồm các thông tin:
    - @param documentId // hash of LC number
    - @param nonce
    - @param from // address of proposer
  - Thực hiện get requestId (amend request).
  - Get content of amend request.
  - Sau khi các bước trên passed thì tiến hành fullfill amend request.
