// Code trả về danh sách mã lớp, mã giảng viên, mã môn và bảng tổng hợp số giờ mỗi giảng viên
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

// Mảng chứa mã môn học và số giờ dạy
const course_table = [
    ['PRO224', 10],
    ['PRO220', 10],
    ['PRO1014', 34],
    ['WEB4014', 34],
    ['WEB2041', 12],
    ['WEB105', 34],
    ['PMA1011', 34],
    ['PRO2052', 10],
    ['PRO227', 10],
    ['PRO2261', 10],
    ['GAM108', 12],
    ['GAM201', 34],
    ['GAM110', 34],
    ['PRO2241', 10],
    ['PRO2201', 10],
    ['WEB503', 34],
    ['WEB2063', 12],
    ['WEB1043', 34],
    ['GAM301', 34],
    ['GAM109', 34],
    ['GAM104', 34],
    ['WEB2014', 34],
    ['WEB3014', 34],
    ['WEB2072', 34],
    ['AND102', 34],
    ['WEB3023', 34],
    ['WEB2053', 34],
    ['AND103', 34],
    ['CRO101', 34],
    ['WEB106', 34],
    ['PRO226', 10],
    ['PRO1122', 34],
    ['KOT1041', 34],
    ['CRO102', 34],
    ['MOB2041', 12],
    ['GAM202', 34],
    ['GAM106', 34],
    ['WEB2091', 34],
    ['WEB1022', 34],
    ['WEB2034', 34],
    ['AND101', 34],
    ['WEB1013', 34],
    ['PRO124', 34],
    ['WEB501', 34],
    ['WEB2081', 34],
    ['WEB502', 34],
    ['GAM107', 34],
    ['GAM105', 34],
    ['WEB108', 34]
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
            const url = `https://gv.poly.edu.vn/teacher/activity?area_id=&day=2025-05-01&keyword=${keyword}`;
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

    // Tạo bảng tổng hợp số giờ dạy
    const summaryMap = new Map();
    result_data.forEach(line => {
        const [_, col7, col8] = line.split('\t'); // Tách dòng thành cột 6, 7, 8
        // Tìm số giờ trong course_table
        const course = course_table.find(([code]) => code === col8);
        const hours = course ? course[1] : 0; // Nếu không tìm thấy, số giờ là 0
        // Cộng dồn số giờ theo col7
        if (summaryMap.has(col7)) {
            summaryMap.set(col7, summaryMap.get(col7) + hours);
        } else {
            summaryMap.set(col7, hours);
        }
    });

    // Tạo mảng kết quả tổng hợp
    const summary_data = Array.from(summaryMap.entries()).map(([key, value]) => `${key}\t${value}`);

    // Xuất kết quả ra console
    console.log('Danh sách dữ liệu gốc:');
    console.log(result_data.join('\n'));
    console.log('\nBảng tổng hợp số giờ:');
    console.log(summary_data.join('\n'));

    // Ghi kết quả lên trang web
    document.write(`
        <h3>Danh sách dữ liệu gốc</h3>
        <textarea style="width: 100%; height: 200px;">${result_data.join('\n')}</textarea>
        <h3>Bảng tổng hợp số giờ</h3>
        <textarea style="width: 100%; height: 200px;">${summary_data.join('\n')}</textarea>
    `);
    console.log('Xử lý hoàn tất');
}

// Gọi hàm để thực thi
extractTableData();