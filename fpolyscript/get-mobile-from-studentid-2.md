//Code trả về danh sách có cấu trúc mỗi dòng gồm mssv[tab]số điện thoại
//Mở trang chủ ap https://gv.poly.edu.vn/admin, bật F12 vào tab Console dán code vào
//Điền danh sách sinh viên tương ứng vào đây

var list_msv = [
    'PH22708',
    'PH37235',
    'PH58577',
    'PH58953'
];





	var list_phone = [];
const timer = ms => new Promise(res => setTimeout(res, ms));


	const getData = async()=>{

		var itemsProcessed = 0;

		await list_msv.forEach(async (msv)=>{

				let res = await fetch("https://gv.poly.edu.vn/student/info?user_code="+ msv);

		    	html = await res.text();

		    	var doc = new DOMParser().parseFromString(html, "text/html");
			    // document.write("<textarea>" + html + "</textarea>");
			    // console.log(doc.querySelectorAll('label'));
			    let row =  doc.querySelectorAll('label');
			    var row_2 = '';
			    row.forEach((el)=>{
			        // console.log(el.innerText);
			        if(el.innerText == 'Mã sinh viên'){
			            row_2 += "" +  el.nextElementSibling.firstElementChild.value;
			        }
			        

			        if(el.innerText == 'Số điện thoại'){
			            row_2 += "	" +  el.nextElementSibling.firstElementChild.value;
			            list_phone.push(row_2);
			            itemsProcessed++;

			            if(itemsProcessed === list_msv.length) {
					      callback();
					    }
			        }
			        // console.log(el.nextElementSibling.firstElementChild);
			        // cons

				});


			console.log(list_phone);
			await timer(10000); 

		});

	        

}
function callback () { 
	console.log('all done'); 

document.write("<textarea>" + list_phone.join("\n") + "</textarea>");
}

getData();
