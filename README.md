# CurrencyCode
Obtain data from XML, process data with MS Access, calculate whole amount.

















using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;
using System.Xml;
using System.Data.OleDb;

namespace DovizBurosuArayuzu
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }

        OleDbConnection baglanti = new OleDbConnection(@"Provider=Microsoft.Jet.OLEDB.4.0;Data Source= C:\Users\apehleg\Desktop\Eğitimler\Udemy Projeler\Örnekler\DövizBürosu\DBKASA.mdb");

        public void listele()
        {
            baglanti.Open();
            OleDbCommand komut = new OleDbCommand("Select * From TBLDOVIZ", baglanti);
            DataTable dt = new DataTable();
            OleDbDataAdapter da = new OleDbDataAdapter(komut);
            da.Fill(dt);
            dataGridView1.DataSource = dt;
            baglanti.Close();
        }

        public void guncelle()
        {
            baglanti.Open();
            OleDbCommand kmt = new OleDbCommand("Select SUM(DOLAR),SUM(EURO),SUM(GBP),SUM(TL) from TBLDOVIZ", baglanti);
            OleDbDataReader dr = kmt.ExecuteReader();
            while (dr.Read())
            {
                textBox1.Text = dr[0].ToString();
                textBox2.Text = dr[1].ToString();
                textBox3.Text = dr[2].ToString();
                textBox4.Text = dr[3].ToString();
            }
            baglanti.Close();
        }

        private void Form1_Load(object sender, EventArgs e)
        {
            baglanti.Open();
            OleDbCommand komut = new OleDbCommand("Select * From TBLDOVIZ", baglanti);            
            DataTable dt = new DataTable();
            OleDbDataAdapter da = new OleDbDataAdapter(komut);
            da.Fill(dt);
            dataGridView1.DataSource = dt;            
            baglanti.Close();
            guncelle();      
        
        }       

        private void groupBox1_Enter(object sender, EventArgs e)
        {
            string bugun = "https://www.tcmb.gov.tr/kurlar/today.xml";
            var xmldosya = new XmlDocument();
            xmldosya.Load(bugun);

            string dolaralis = xmldosya.SelectSingleNode("Tarih_Date/Currency[@Kod='USD']/BanknoteBuying").InnerXml;
            LblUSDAlis.Text = dolaralis;

            string dolarsatis = xmldosya.SelectSingleNode("Tarih_Date/Currency[@Kod='USD']/BanknoteSelling").InnerXml;
            LblUSDSatis.Text = dolarsatis;

            string euroalis = xmldosya.SelectSingleNode("Tarih_Date/Currency[@Kod='EUR']/BanknoteBuying").InnerXml;
            LblEUROAlis.Text = euroalis;

            string eurosatis = xmldosya.SelectSingleNode("Tarih_Date/Currency[@Kod='EUR']/BanknoteSelling").InnerXml;
            LblEUROSatis.Text = eurosatis;

            string sterlinalis = xmldosya.SelectSingleNode("Tarih_Date/Currency[@Kod='GBP']/BanknoteBuying").InnerXml;
            LblGBPAlis.Text = sterlinalis;

            string sterlinsatis = xmldosya.SelectSingleNode("Tarih_Date/Currency[@Kod='GBP']/BanknoteSelling").InnerXml;
            LblGBPSatis.Text = sterlinsatis;
        }

        private void BtnUSDAl_Click(object sender, EventArgs e)
        {
            CmbKurAdi.Text = "DOLAR AL";
            TxtKurFiyat.Text = LblUSDAlis.Text;
        }

        private void BtnUSDSat_Click(object sender, EventArgs e)
        {
            CmbKurAdi.Text = "DOLAR SAT";
            TxtKurFiyat.Text = LblUSDSatis.Text;
        }

        private void BtnEUROAL_Click(object sender, EventArgs e)
        {
            CmbKurAdi.Text = "EURO AL";
            TxtKurFiyat.Text = LblEUROAlis.Text;
        }

        private void BtnEUROSat_Click(object sender, EventArgs e)
        {
            CmbKurAdi.Text = "EURO SAT";
            TxtKurFiyat.Text = LblEUROSatis.Text;
        }

        private void BtnGBPAl_Click(object sender, EventArgs e)
        {
            CmbKurAdi.Text = "GBP AL";
            TxtKurFiyat.Text = LblGBPAlis.Text;
        }

        private void BtnGBPSAT_Click(object sender, EventArgs e)
        {
            CmbKurAdi.Text = "GBP SAT";
            TxtKurFiyat.Text = LblGBPSatis.Text;
        }

        private void button1_Click(object sender, EventArgs e)
        {
            Application.Exit();
        }

        private void button3_Click(object sender, EventArgs e)
        {
            double kur, miktar, tutar;
            kur = Convert.ToDouble(TxtKurFiyat.Text);
            miktar =Convert.ToDouble(TxtMiktar.Text);
            double eksimiktar = -miktar;            
            tutar = kur * miktar;
            double eksitutar = -tutar;
            TxtTutar.Text = tutar.ToString();
            string isim;
            isim = CmbKurAdi.Text;
            //DOLAR
            if (CmbKurAdi.Text == "DOLAR SAT")
            {
                baglanti.Open();
                OleDbCommand komut2 = new OleDbCommand("Insert into TBLDOVIZ (TL,DOLAR) values (@P1,@P2)", baglanti);
                komut2.Parameters.AddWithValue("@P1", TxtTutar.Text);
                komut2.Parameters.AddWithValue("@P2", eksimiktar);
                komut2.ExecuteNonQuery();
                baglanti.Close();
                listele();
                guncelle();
            }
            if (CmbKurAdi.Text == "DOLAR AL")
            {
                baglanti.Open();
                OleDbCommand komut2 = new OleDbCommand("Insert into TBLDOVIZ (TL,DOLAR) values (@P1,@P2)", baglanti);
                komut2.Parameters.AddWithValue("@P1", eksitutar);
                komut2.Parameters.AddWithValue("@P2", miktar);
                komut2.ExecuteNonQuery();
                baglanti.Close();
                listele();
                guncelle();
            }
            //EURO

            if (CmbKurAdi.Text == "EURO SAT")
            {
                baglanti.Open();
                OleDbCommand komut2 = new OleDbCommand("Insert into TBLDOVIZ (TL,EURO) values (@P1,@P2)", baglanti);
                komut2.Parameters.AddWithValue("@P1", TxtTutar.Text);
                komut2.Parameters.AddWithValue("@P2", eksimiktar);
                komut2.ExecuteNonQuery();
                baglanti.Close();
                listele();
                guncelle();
            }
            if (CmbKurAdi.Text == "EURO AL")
            {
                baglanti.Open();
                OleDbCommand komut2 = new OleDbCommand("Insert into TBLDOVIZ (TL,EURO) values (@P1,@P2)", baglanti);
                komut2.Parameters.AddWithValue("@P1", eksitutar);
                komut2.Parameters.AddWithValue("@P2", miktar);
                komut2.ExecuteNonQuery();
                baglanti.Close();
                listele();
                guncelle();
            }
            //GBP
            if (CmbKurAdi.Text == "GBP SAT")
            {
                baglanti.Open();
                OleDbCommand komut2 = new OleDbCommand("Insert into TBLDOVIZ (TL,GBP) values (@P1,@P2)", baglanti);
                komut2.Parameters.AddWithValue("@P1", TxtTutar.Text);
                komut2.Parameters.AddWithValue("@P2", eksimiktar);
                komut2.ExecuteNonQuery();
                baglanti.Close();
                listele();
                guncelle();
            }
            if (CmbKurAdi.Text == "GBP AL")
            {
                baglanti.Open();
                OleDbCommand komut2 = new OleDbCommand("Insert into TBLDOVIZ (TL,GBP) values (@P1,@P2)", baglanti);
                komut2.Parameters.AddWithValue("@P1", eksitutar);
                komut2.Parameters.AddWithValue("@P2", miktar);
                komut2.ExecuteNonQuery();
                baglanti.Close();
                listele();
                guncelle();
            }
        }

        private void TxtKurFiyat_TextChanged(object sender, EventArgs e)
        {
            TxtKurFiyat.Text = TxtKurFiyat.Text.Replace(".", ",");
        }

        private void button4_Click(object sender, EventArgs e)
        {
            double kur =Convert.ToDouble(TxtKurFiyat.Text);
            int tutar = Convert.ToInt32(TxtTutar.Text);
            int miktar = Convert.ToInt32(tutar / kur);            
            TxtMiktar.Text = miktar.ToString();            
            double kalan;
            kalan = tutar % kur;
            TxtKalan.Text = kalan.ToString();
            double fark;
            fark = tutar - kalan;
            double eksimiktar = -1*miktar;
            double eksifark = -fark;            
            //DOLAR
            if (CmbKurAdi.Text == "DOLAR SAT")
            {
                baglanti.Open();
                OleDbCommand komut2 = new OleDbCommand("Insert into TBLDOVIZ (TL,DOLAR) values (@P1,@P2)", baglanti);
                komut2.Parameters.AddWithValue("@P1", fark);
                komut2.Parameters.AddWithValue("@P2", eksimiktar);
                komut2.ExecuteNonQuery();
                baglanti.Close();
                listele();
                guncelle();
            }
            if (CmbKurAdi.Text == "DOLAR AL")
            {
                baglanti.Open();
                OleDbCommand komut2 = new OleDbCommand("Insert into TBLDOVIZ (TL,DOLAR) values (@P1,@P2)", baglanti);
                komut2.Parameters.AddWithValue("@P1", eksifark);
                komut2.Parameters.AddWithValue("@P2", miktar);
                komut2.ExecuteNonQuery();
                baglanti.Close();
                listele();
                guncelle();
            }
            //EURO
            if (CmbKurAdi.Text == "EURO SAT")
            {
                baglanti.Open();
                OleDbCommand komut2 = new OleDbCommand("Insert into TBLDOVIZ (TL,EURO) values (@P1,@P2)", baglanti);
                komut2.Parameters.AddWithValue("@P1", fark);
                komut2.Parameters.AddWithValue("@P2", eksimiktar);
                komut2.ExecuteNonQuery();
                baglanti.Close();
                listele();
                guncelle();
            }
            if (CmbKurAdi.Text == "EURO AL")
            {
                baglanti.Open();
                OleDbCommand komut2 = new OleDbCommand("Insert into TBLDOVIZ (TL,EURO) values (@P1,@P2)", baglanti);
                komut2.Parameters.AddWithValue("@P1", eksifark);
                komut2.Parameters.AddWithValue("@P2", miktar);
                komut2.ExecuteNonQuery();
                baglanti.Close();
                listele();
                guncelle();
            }
            //GBT
            if (CmbKurAdi.Text == "GBP SAT")
            {
                baglanti.Open();
                OleDbCommand komut2 = new OleDbCommand("Insert into TBLDOVIZ (TL,GBP) values (@P1,@P2)", baglanti);
                komut2.Parameters.AddWithValue("@P1", fark);
                komut2.Parameters.AddWithValue("@P2", eksimiktar);
                komut2.ExecuteNonQuery();
                baglanti.Close();
                listele();
                guncelle();
            }
            if (CmbKurAdi.Text == "GBP AL")
            {
                baglanti.Open();
                OleDbCommand komut2 = new OleDbCommand("Insert into TBLDOVIZ (TL,GBP) values (@P1,@P2)", baglanti);
                komut2.Parameters.AddWithValue("@P1", eksifark);
                komut2.Parameters.AddWithValue("@P2", miktar);
                komut2.ExecuteNonQuery();
                baglanti.Close();
                listele();
                guncelle();
            }

        } 
        

        //
        private void button2_Click(object sender, EventArgs e)
        {
            baglanti.Open();
            OleDbCommand kmt = new OleDbCommand("Select SUM(DOLAR),SUM(EURO),SUM(GBP),SUM(TL) from TBLDOVIZ", baglanti);
            OleDbDataReader dr = kmt.ExecuteReader();
            while (dr.Read())
            {
                textBox1.Text = dr[0].ToString();
                textBox2.Text = dr[1].ToString();
                textBox3.Text = dr[2].ToString();
                textBox4.Text = dr[3].ToString();
            }
            baglanti.Close();
        }

        private void button5_Click(object sender, EventArgs e)
        {
            CmbKurAdi.Text = "";
            TxtKalan.Text = "";
            TxtKurFiyat.Text = "";
            TxtMiktar.Text = "";
            TxtTutar.Text = "";
        }
    }
}
