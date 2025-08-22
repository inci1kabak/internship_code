-- Kullanıcı bilgilerini tutan tabloyu oluşturma
CREATE TABLE Kullanicilar (
    kullanici_id NUMBER PRIMARY KEY,
    ad VARCHAR2(50),
    soyad VARCHAR2(50),
    email VARCHAR2(100),
    kayit_tarihi DATE
);

-- Sınav sonuçlarını tutan tabloyu oluşturma
CREATE TABLE SinavSonuclari (
    sonuc_id NUMBER PRIMARY KEY,
    kullanici_id NUMBER,
    ders VARCHAR2(50),
    puan NUMBER,
    CONSTRAINT fk_kullanici FOREIGN KEY (kullanici_id) REFERENCES Kullanicilar(kullanici_id)
);

-- Veri ekleme (INSERT örneği)
INSERT INTO Kullanicilar (kullanici_id, ad, soyad, email, kayit_tarihi)
VALUES (1, 'Ali', 'Yılmaz', 'ali.yilmaz@example.com', SYSDATE);

INSERT INTO SinavSonuclari (sonuc_id, kullanici_id, ders, puan)
VALUES (101, 1, 'Veritabanı', 85);

-- Basit SELECT sorgusu
SELECT ad, soyad, email 
FROM Kullanicilar;

-- WHERE koşulu ile filtreleme
SELECT ad, soyad, puan 
FROM Kullanicilar k
JOIN SinavSonuclari s ON k.kullanici_id = s.kullanici_id
WHERE puan > 70;

-- GROUP BY ile ortalama puan hesaplama
SELECT ders, AVG(puan) AS ortalama_puan
FROM SinavSonuclari
GROUP BY ders;

-- ORDER BY ile sıralama
SELECT ad, soyad, puan
FROM Kullanicilar k
JOIN SinavSonuclari s ON k.kullanici_id = s.kullanici_id
ORDER BY puan DESC;

----------------------------------------------------------

-- Ürün tablosu oluşturma
CREATE TABLE Urunler (
    urun_id INT PRIMARY KEY AUTO_INCREMENT,
    urun_adi VARCHAR(100),
    fiyat DECIMAL(10,2)
);

-- Depo tablosu oluşturma
CREATE TABLE Depolar (
    depo_id INT PRIMARY KEY AUTO_INCREMENT,
    depo_adi VARCHAR(100)
);

-- Ürünlerin hangi depoda olduğunu gösteren ilişki tablosu
CREATE TABLE UrunDepo (
    urun_id INT,
    depo_id INT,
    FOREIGN KEY (urun_id) REFERENCES Urunler(urun_id),
    FOREIGN KEY (depo_id) REFERENCES Depolar(depo_id)
);

-- Veri ekleme (INSERT)
INSERT INTO Urunler (urun_adi, fiyat) VALUES ('Laptop', 25000);
INSERT INTO Depolar (depo_adi) VALUES ('Ankara Depo');
INSERT INTO UrunDepo (urun_id, depo_id) VALUES (1, 1);

-- Veri güncelleme (UPDATE)
UPDATE Urunler
SET fiyat = 23000
WHERE urun_id = 1;

-- Veri silme (DELETE)
DELETE FROM Urunler
WHERE urun_id = 1;

-- INNER JOIN: Ürünlerin hangi depoda olduğunu görmek
SELECT u.urun_adi, d.depo_adi
FROM Urunler u
INNER JOIN UrunDepo ud ON u.urun_id = ud.urun_id
INNER JOIN Depolar d ON ud.depo_id = d.depo_id;

-- LEFT JOIN: Deposu olmayan ürünleri de göstermek
SELECT u.urun_adi, d.depo_adi
FROM Urunler u
LEFT JOIN UrunDepo ud ON u.urun_id = ud.urun_id
LEFT JOIN Depolar d ON ud.depo_id = d.depo_id;

-- RIGHT JOIN: Ürünü olmayan depoları göstermek
SELECT u.urun_adi, d.depo_adi
FROM Urunler u
RIGHT JOIN UrunDepo ud ON u.urun_id = ud.urun_id
RIGHT JOIN Depolar d ON ud.depo_id = d.depo_id;

-- İndeks oluşturma (sorgu hızını artırmak için)
CREATE INDEX idx_urun_adi ON Urunler(urun_adi);

import React from "react";
import { Formik, Form, Field, ErrorMessage } from "formik";
import * as Yup from "yup";

const RegisterForm = () => {
  // Başlangıç değerleri (state)
  const initialValues = {
    fullName: "",
    email: "",
    password: "",
    phoneNumber: ""
  };

  // Yup doğrulama şeması
  const validationSchema = Yup.object({
    fullName: Yup.string()
      .required("İsim zorunludur")
      .max(50, "En fazla 50 karakter olabilir"),
    email: Yup.string()
      .email("Geçerli bir email giriniz")
      .required("Email zorunludur"),
    password: Yup.string()
      .min(6, "Şifre en az 6 karakter olmalı")
      .required("Şifre zorunludur"),
    phoneNumber: Yup.string()
      .matches(/^[0-9]{10}$/, "Telefon numarası 10 haneli olmalıdır")
      .required("Telefon numarası zorunludur")
  });

  // Form gönderildiğinde backend’e JSON gönderimi
  const onSubmit = (values) => {
    console.log("Gönderilen Veriler:", values);
    fetch("http://localhost:5000/api/register", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(values)
    })
      .then((res) => res.json())
      .then((data) => console.log("Backend cevabı:", data));
  };

  return (
    <Formik
      initialValues={initialValues}
      validationSchema={validationSchema}
      onSubmit={onSubmit}
    >
      <Form>
        <div>
          <label>Ad Soyad:</label>
          <Field type="text" name="fullName" />
          <ErrorMessage name="fullName" component="div" />
        </div>

        <div>
          <label>Email:</label>
          <Field type="email" name="email" />
          <ErrorMessage name="email" component="div" />
        </div>

        <div>
          <label>Şifre:</label>
          <Field type="password" name="password" />
          <ErrorMessage name="password" component="div" />
        </div>

        <div>
          <label>Telefon Numarası:</label>
          <Field type="text" name="phoneNumber" />
          <ErrorMessage name="phoneNumber" component="div" />
        </div>

        <button type="submit">Kayıt Ol</button>
      </Form>
    </Formik>
  );
};

export default RegisterForm;

-----------------------------------------------------
-- 1. B-Tree Index Örneği
-- Products tablosunda fiyat alanına indeks ekleme
CREATE INDEX idx_price ON Products(price);

-- İndeksin kullanıldığı sorgu
SELECT * 
FROM Products 
WHERE price > 100;

-- 2. Fulltext Index Örneği
-- Blog tablosunda içerik alanına fulltext indeks ekleme
CREATE FULLTEXT INDEX idx_content ON Blog(content);

-- Fulltext arama sorgusu
SELECT * 
FROM Blog 
WHERE MATCH(content) AGAINST('veritabanı');

-- 3. Composite Index Örneği
-- Orders tablosunda müşteri ve tarih alanlarına composite index ekleme
CREATE INDEX idx_customer_date ON Orders(customer_id, order_date);

-- İndeksin avantaj sağladığı sorgu
SELECT * 
FROM Orders 
WHERE customer_id = 123 AND order_date > '2024-01-01';

// users tablosuna yeni alan ekleme
await queryInterface.addColumn("users", "phone_number", {
  type: Sequelize.STRING,
  allowNull: false
});

await queryInterface.addColumn("users", "is_phone_verified", {
  type: Sequelize.BOOLEAN,
  defaultValue: false
});

const express = require("express");
const router = express.Router();
const { User } = require("../models");

// Mock SMS doğrulama servisi
const sendMockSMS = (phoneNumber) => {
  console.log(`SMS doğrulama kodu gönderildi: ${phoneNumber}`);
  return true;
};

// Kullanıcı kayıt endpoint’i
router.post("/register", async (req, res) => {
  const { email, password, phone_number } = req.body;

  if (!phone_number || !/^[0-9]{10}$/.test(phone_number)) {
    return res.status(400).json({ error: "Geçerli bir telefon numarası giriniz" });
  }

  try {
    const user = await User.create({
      email,
      password,
      phone_number,
      is_phone_verified: false
    });

    // Mock SMS gönderimi
    sendMockSMS(phone_number);

    res.status(201).json({ message: "Kullanıcı başarıyla oluşturuldu", user });
  } catch (error) {
    res.status(500).json({ error: "Sunucu hatası", details: error.message });
  }
});

module.exports = router;

----------------------------------------------
Güvensiz Kullanım (XSS Açığı):

// API'den gelen kullanıcı verisi
const userComment = "<img src=x onerror=alert('XSS!')>";

// Tehlikeli: veriyi doğrudan DOM'a eklemek
document.getElementById("commentBox").innerHTML = userComment;

Kod Örneği – DOMPurify ile Güvenli Kullanım:
import DOMPurify from "dompurify";

const userComment = "<img src=x onerror=alert('XSS!')>";

// Güvenli: DOMPurify ile temizleyerek ekleme
document.getElementById("commentBox").innerHTML = DOMPurify.sanitize(userComment);

Kod Örneği – Magic Number Kullanımı:
if (user.age > 18) {
  giveAccess();
}
Kod Örneği – Constants ile Daha Okunabilir Kod
const MINIMUM_AGE = 18;

if (user.age > MINIMUM_AGE) {
  giveAccess();
}






























