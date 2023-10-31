# CRUD
hasil mini project membuat aplikasi mengelola peminjaman buku
# FILE package.JSON
file ini berisi konfigurasi utama dalam mini project
{
  "name": "web-server-native",
  "version": "1.0.0",
  "description": "CRUD",
  "main": "app.js",
  "type":"module",
  "scripts": {
    "dev": "nodemon app.js"
  },
  "author": "Fadillah Ahmad Avicenna",
  "license": "ISC",
  "dependencies": {
    "nodemon": "^3.0.1"
  }
}

# FILE data.js
file ini berisi data data awal yang di input sebelum CRUD

const users=[
{
    nama:'cenna',
    umur:20,
    jurusan:'Sistem Informasi',
    buku:'Laskar Pelangi',
},
{
    nama:'raihan',
    umur:20,
    jurusan:'Informatika',
    buku:'Dilan 1990',
},
{
    nama:'rifqi',
    umur:31,
    jurusan:'Hukum',
    buku:'Stoic',
},
];
export default users;

# FILE app.js
file ini berisi program yang akan melakukan fungsi CRUD

import http from 'http';
import data from './data.js';

const requestListener = (req, res) => {
    const method=req["method"];
    const url=req.url.split('/')[1];
    const prmtr=req.url.split('/')[2];

    res.setHeader('Content-type', 'application/json');
    //method GET
    if(method==='GET'){
        switch(url){
            case 'user':
                const responseJSON={
                    message:"data berhasil diambil",
                    data:data,
                };
                res.end(JSON.stringify(responseJSON));
                break;
        }
    }
    //method POST
    if(method==='POST'){
        let requestBody='';
                req.on('data', (data)=>{
                    requestBody+=data;
                    console.log(requestBody);
                });

        switch(url){
            case'user':
                req.on('end', ()=>{
                    requestBody=JSON.parse(requestBody);
                    data.push(requestBody);
                    const responseJSON={
                        message:"data berhasil ditambah",
                        data:data,
                    };
                    return res.end(JSON.stringify(responseJSON));
                });
                break;
                
        }

    }
    //method PUT
    if (method === 'PUT') {
        let requestBody = '';
        req.on('data', (data) => {
            requestBody += data;
        });

        switch (url) {
            case 'user':
                req.on('end', () => {
                    const parsedRequestBody = JSON.parse(requestBody);
                    const targetUsername = prmtr; // Menggunakan parameter sebagai nama pengguna yang akan diperbarui

                    const targetIndex = data.findIndex((user) => user.nama === targetUsername);
                    if (targetIndex !== -1) {
                        data[targetIndex] = { ...data[targetIndex], ...parsedRequestBody };
                        const responseJSON = {
                            message: "Data berhasil diupdate",
                            data: data,
                        };
                        res.end(JSON.stringify(responseJSON));
                    } else {
                        const responseJSON = {
                            message: "Data tidak ditemukan",
                            data: data,
                        };
                        res.end(JSON.stringify(responseJSON));
                    }
                });
                break;
        }
    }
    //method DELETE
    if (method === 'DELETE') {
        switch (url) {
            case 'user':
                const index = data.findIndex((user) => user.nama === prmtr);
                if (index !== -1) {
                    data.splice(index, 1);
                    const responseJSON = {
                        message: "Data berhasil dihapus",
                        data: data,
                    };
                    res.end(JSON.stringify(responseJSON));
                } else {
                    const responseJSON = {
                        message: "Data tidak ditemukan",
                        data: data,
                    };
                    res.end(JSON.stringify(responseJSON));
                }
                break;
        }
    }
};

const app= http.createServer(requestListener);
const port = 3000;
app.listen(port, () => {
    console.log(`Server running on port ${port}`); 
});
