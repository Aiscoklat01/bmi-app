import streamlit as st
import matplotlib.pyplot as plt
import pandas as pd
import io

# =====================
# Konfigurasi Aplikasi
# =====================
st.set_page_config(page_title="Aplikasi Kesihatan Ringkas", page_icon="💪", layout="centered")

st.title("💪 Aplikasi Kesihatan Ringkas")
st.markdown("Pantau **BMI**, **kalori harian**, dan **pemakanan sihat** anda di satu tempat.")

# -------------------
# Input Nama
# -------------------
st.sidebar.header("👤 Maklumat Pengguna")
nama = st.sidebar.text_input("Nama:")
gender = st.sidebar.selectbox("Jantina:", ["Lelaki", "Perempuan"])
default_cal = 2500 if gender == "Lelaki" else 2000
target_cal = st.sidebar.number_input("🎯 Sasaran Kalori Harian (kcal):", value=default_cal)

st.markdown("---")

# =====================
# Tabs Navigasi
# =====================
tab1, tab2, tab3, tab4 = st.tabs(["📏 BMI", "🔥 Kalori", "🍛 Makanan", "📊 Data & Muat Turun"])

# =====================
# Tab 1 – BMI
# =====================
with tab1:
    st.header("📏 Kalkulator BMI")

    col1, col2 = st.columns(2)
    with col1:
        berat = st.number_input("Berat (kg)", min_value=1.0, format="%.1f")
    with col2:
        tinggi = st.number_input("Tinggi (cm)", min_value=1.0, format="%.1f")

    bmi = None
    kategori_bmi = ""
    if berat and tinggi:
        bmi = berat / ((tinggi / 100) ** 2)
        st.subheader(f"BMI: {bmi:.2f}")

        if bmi < 18.5:
            kategori_bmi = "Kurus"
        elif bmi < 25:
            kategori_bmi = "Normal"
        elif bmi < 30:
            kategori_bmi = "Berlebihan berat"
        else:
            kategori_bmi = "Obes"

        st.success(f"Status: **{kategori_bmi}**")

        # Cadangan makanan ikut kategori
        st.markdown("### 🍽️ Cadangan Pemakanan")
        if kategori_bmi == "Kurus":
            st.info("- Tambah makanan berprotein tinggi (ayam, telur, susu)\n- Minum smoothie buah & oat")
        elif kategori_bmi == "Normal":
            st.success("- Kekalkan diet seimbang\n- Banyakkan sayur & air kosong")
        elif kategori_bmi == "Berlebihan berat":
            st.warning("- Kurangkan makanan bergoreng & manis\n- Lebihkan senaman ringan")
        else:
            st.error("- Elakkan makanan segera\n- Fokus pada diet sihat dan aktiviti fizikal")

# =====================
# Tab 2 – Kalori
# =====================
with tab2:
    st.header("🔥 Pengira Kalori Harian")

    eaten = st.number_input("Masukkan jumlah kalori dimakan (kcal):", min_value=0, value=0)
    baki = target_cal - eaten

    if baki > 0:
        st.info(f"Kalori belum cukup: {baki} kcal lagi")
    elif baki < 0:
        st.error(f"Terlampau {abs(baki)} kcal dari sasaran!")
    else:
        st.success("Tepat cukup kalori!")

    # Kad ringkasan
    colA, colB, colC = st.columns(3)
    colA.metric("Kalori Dimakan", f"{eaten} kcal")
    colB.metric("Sasaran", f"{target_cal} kcal")
    colC.metric("Baki", f"{baki} kcal")

# =====================
# Tab 3 – Makanan
# =====================
with tab3:
    st.header("🍛 Pilih Makanan Anda")

    senarai_makanan = {
        "Nasi Putih (1 pinggan)": 200,
        "Ayam Goreng": 250,
        "Ikan Bakar": 180,
        "Telur Rebus": 80,
        "Sayur Tumis": 100,
        "Teh Ais": 150,
        "Kopi O": 50,
        "Roti Canai": 300,
        "Mee Goreng": 400,
        "Nasi Lemak": 500,
        "Air Kosong": 0
    }

    pilihan = st.multiselect(
        "Pilih makanan yang telah dimakan:",
        options=list(senarai_makanan.keys())
    )

    kalori_makanan = sum(senarai_makanan[m] for m in pilihan)
    lain = st.number_input("Tambah kalori lain (lain-lain makanan):", min_value=0, value=0)

    total_makan = kalori_makanan + lain
    st.subheader(f"Jumlah Kalori daripada makanan: **{total_makan} kcal**")

    # Visual graf
    if total_makan > 0:
        fig, ax = plt.subplots(figsize=(5, 3))
        bars = ax.bar(["Dimakan", "Baki"], [total_makan, max(target_cal - total_makan, 0)],
                      color=["orange", "green"])
        ax.set_ylabel("Kalori (kcal)")
        ax.set_title("Kalori Harian (Makanan)")
        for bar in bars:
            h = bar.get_height()
            ax.annotate(f'{h:.0f}', xy=(bar.get_x() + bar.get_width()/2, h), xytext=(0,3),
                        textcoords="offset points", ha='center', va='bottom')
        st.pyplot(fig)

# =====================
# Tab 4 – Data & Muat Turun
# =====================
with tab4:
    st.header("📊 Ringkasan & Muat Turun")

    if nama and tinggi and berat:
        df = pd.DataFrame({
            "Nama": [nama],
            "Jantina": [gender],
            "Berat (kg)": [berat],
            "Tinggi (cm)": [tinggi],
            "BMI": [round(bmi, 2) if bmi else None],
            "Status BMI": [kategori_bmi],
            "Kalori Dimakan": [eaten],
            "Kalori Makanan": [total_makan],
            "Sasaran Kalori": [target_cal],
            "Baki Kalori": [baki],
            "Makanan Dipilih": [", ".join(pilihan)]
        })

        st.dataframe(df)

        csv = df.to_csv(index=False).encode("utf-8")
        st.download_button(
            label="📥 Muat Turun Data (CSV)",
            data=csv,
            file_name=f"data_kesihatan_{nama}.csv",
            mime="text/csv"
        )

    else:
        st.info("Lengkapkan maklumat anda di tab sebelumnya untuk melihat ringkasan.")

st.markdown("---")
st.caption("Dibangunkan dengan ❤️ menggunakan Streamlit")
