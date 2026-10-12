# Document/Code Source Baseline — V3.0

| Basis | Path in ZIP | How used |
| --- | --- | --- |
| Approved architecture | document/architectures/architecture_v3.3.md | Do not redesign runtime / event contracts. |
| Approved module specification | document/specifications/modules/modules_specification_v1.3.md | Flow requirements traced to M01–M14. |
| Approved DB schema | document/schema/v1.0/anti_scam_schema_overall_v1.0.md | No silent schema redesign; changes need explicit migration/ADR. |
| Release dates only | document/plan/v2.1/Timeline_full_Anti_Scam_2026_2027_V2.1.md | Reuse only fixed release dates; task breakdown rewritten from scratch. |
| Merged W09 code snapshot | Scam-Risk-Detector(1).zip | Static inspection only; current remote branch/CI status not verified. |


## Scope decision

- Architecture V3.3 / Modules Spec V1.3 / Schema V1.0 được coi là đã duyệt và không được thay đổi tự động chỉ để vừa lịch.
- Release dates kế thừa nguyên vẹn từ kế hoạch cũ. Breakdown W09/W10 task cũ KHÔNG phải backlog source of truth.
- W09 components có thể được reuse; việc nào đã kiểm chứng DONE từ code phải ghi evidence và cập nhật remaining story points, không làm lại.
- Nếu phát hiện bất đồng giữa spec/repo, tạo issue quyết định với 4 thông tin: conflict, impact, option, approver; không viết implementation suy đoán.
- Các quan sát repo là snapshot ZIP được upload, không phải tình trạng nhánh GitHub sau thời điểm upload.
