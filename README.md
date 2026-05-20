# switch-games-data

Repo dữ liệu tập trung cho [Switch Games Manager](https://github.com/uongsuadaubung/switch-games).

> **Chỉ admin mới push dữ liệu. Người dùng không cần làm gì — app tự fetch.**

## Cấu trúc

```
switch-games-data/
├── source/
│   └── *.zip               ← Admin push file ZIP HTML bất kỳ vào đây
├── data/
│   └── games.json          ← Tự động tạo bởi GitHub Actions
├── images/
│   └── *.jpg               ← Ảnh game, tự động tải bởi GitHub Actions
├── scripts/
│   ├── parse_zip.py        ← Parser trích xuất dữ liệu từ ZIP
│   └── download_images.py  ← Tải ảnh game và cập nhật image_url trong games.json
└── .github/workflows/
    └── parse.yml           ← Trigger khi push latest.zip
```

## Luồng hoạt động (GitHub Actions)

Khi admin push file ZIP bất kỳ vào thư mục `source/` (ví dụ `source/latest.zip` hoặc `source/data.zip`):

1. **Parse ZIP** → trích xuất toàn bộ game → ghi `data/games.json`
2. **Xoá ZIP** → file ZIP bị xoá sau khi parse xong để giữ repo gọn nhẹ
3. **Download ảnh** → tải ảnh từ URL gốc, convert sang JPG → lưu vào `images/`
4. **Commit & push** → bot commit `games.json`, ảnh mới về repo

> Toàn bộ quá trình mất khoảng 1–2 phút tuỳ số lượng ảnh mới.

## Cập nhật dữ liệu (Admin)

```bash
# Chỉ cần copy file ZIP bất kỳ vào thư mục source/ và push
cp /path/to/new_data.zip source/my_data.zip
git add source/my_data.zip
git commit -m "Update data"
git push
# → GitHub Action tự chạy, parse, tải ảnh, commit lại trong ~1-2 phút
```

## API endpoints (public)

```
# Danh sách game đầy đủ (kèm image_url, links tải, ...)
https://raw.githubusercontent.com/uongsuadaubung/switch-games-data/main/data/games.json

# Ảnh game
https://raw.githubusercontent.com/uongsuadaubung/switch-games-data/main/images/{game_id}.jpg
```

## Chạy parser thủ công (local)

```bash
pip install Pillow

# Parse ZIP → games.json (ZIP sẽ bị xoá sau khi xong)
python scripts/parse_zip.py source/my_data.zip

# Giữ lại ZIP sau khi parse
python scripts/parse_zip.py source/my_data.zip --keep-zip

# Tải ảnh → images/
python scripts/download_images.py

# Tải ảnh nhưng không ghi file (dry run)
python scripts/download_images.py --dry-run
```
