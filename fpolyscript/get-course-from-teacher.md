// Code trả về danh sách mã lớp, mã giảng viên, mã môn
// Mảng chứa danh sách các từ khóa
// Thay bằng các từ khóa thực tế
const list_keywords = [
    'anhnd120',
    'anhnh136',
    'anph21',
    'datlt34',
    'dieulth2',
    'dinhbv',
    'dinhtv7',
    'ductv44',
    'duylh17',
    'halt78',
    'hanglt73',
    'hungcv10',
    'hungnq',
    'kientc',
    'linhdt73',
    'loannt43',
    'loantt',
    'locnv27',
    'longhh7',
    'maidtt',
    'ngocbq',
    'sondt32',
    'thaivm2',
    'thangvd9',
    'thanhht49',
    'thuydt89',
    'thuyvt66',
    'trungnt173',
    'tuannda3',
    'huynh2',
    'minhtht',
    'hoadv21',
    'ngocnv34',
    'ngandt35',
    'hienlv12',
    'lapnv6',
    'trungdq25',
    'duongnt88',
    'truongvv17',
    'hieunc19',
    'nhungnh44',
    'chinhpd5',
    'trungnd87',
    'linhtq25'
];

// Mảng lưu kết quả
let result_data = [];
const timer = ms => new Promise(res => setTimeout(res, ms));

// Hàm lấy dữ liệu từ bảng
const extractTableData = async () => {
    const dataSet = new Set(); // Sử dụng Set để loại bỏ trùng lặp
    let itemsProcessed = 0;

    // Duyệt qua từng từ khóa
    for (const keyword of list_keywords) {
        try {
            // Gửi yêu cầu lấy trang với từ khóa
            const url = `https://gv.poly.edu.vn/teacher/activity?area_id=&day=2025-05-01&keyword=${keyword}`; //thay ngày bắt đầu vào vị trí sau day
            let res = await fetch(url);
            let html = await res.text();

            // Phân tích HTML
            let doc = new DOMParser().parseFromString(html, "text/html");
            const rows = doc.querySelectorAll('table tr');

            // Duyệt qua từng hàng trong bảng
            rows.forEach(row => {
                const cells = row.querySelectorAll('td');
                // Kiểm tra nếu hàng có đủ số lượng td (ít nhất 8 td)
                if (cells.length >= 8) {
                    const sixthCell = cells[5].innerText.trim(); // td thứ 6
                    const seventhCell = cells[6].innerText.trim(); // td thứ 7
                    const eighthCell = cells[7].innerText.trim(); // td thứ 8
                    // Thêm bộ ba dữ liệu vào Set
                    dataSet.add(`${sixthCell}\t${seventhCell}\t${eighthCell}`);
                }
            });

            itemsProcessed++;
            // Đợi một chút để tránh gửi yêu cầu quá nhanh
            await timer(1000);
        } catch (error) {
            console.error(`Lỗi khi xử lý từ khóa ${keyword}:`, error);
            itemsProcessed++;
        }

        // Khi xử lý xong tất cả từ khóa
        if (itemsProcessed === list_keywords.length) {
            callback(dataSet);
        }
    }
};

// Hàm callback để xuất kết quả
function callback(dataSet) {
    // Chuyển Set thành mảng
    result_data = Array.from(dataSet);

    // Xuất kết quả ra console
    console.log(result_data.join('\n'));

    // Ghi kết quả lên trang web
    document.write(`<textarea>${result_data.join('\n')}</textarea>`);
    console.log('Xử lý hoàn tất');
}

// Gọi hàm để thực thi
extractTableData();