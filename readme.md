# 📚 Dokumentasi Project Java GUI + MySQL

Aplikasi ini adalah program GUI berbasis Java Swing yang terhubung dengan database MySQL. Fitur utama meliputi: tambah, tampil, ubah, hapus, cetak, dan load data ke form dari tabel `users`.

---

## 🔸 Import Library Java

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import javax.swing.JOptionPane;
import javax.swing.table.DefaultTableModel;
```

---

## 🔸 Menampilkan Data ke JTable

```java
private DefaultTableModel model;
private String SQL;

public void TampilData() {
    model = new DefaultTableModel();
    model.addColumn("NIM");
    model.addColumn("Nama");
    model.addColumn("Kelas");
    model.addColumn("Jurusan");
    model.addColumn("Username");
    model.addColumn("Password");
    LoadTable.setModel(model);
    Connection conn = DBConnection.getConnection();
    try {
        Statement stmt = conn.createStatement();
        SQL = "SELECT * FROM users";
        ResultSet res = stmt.executeQuery(SQL);
        while (res.next()) {
            model.addRow(new Object[]{
                res.getString("nim"),
                res.getString("nama"),
                res.getString("kelas"),
                res.getString("jurusan"),
                res.getString("username"),
                res.getString("password"),
            });
        }
    } catch (SQLException e) {
        System.out.println(e.getMessage());
    }
}
```

---

## 🔸 Menambahkan Data

```java
private void BtnTambahActionPerformed(java.awt.event.ActionEvent evt) {
    try (Connection conn = DBConnection.getConnection()) {
        String sql = "INSERT INTO users (nim, nama, kelas, jurusan, username, password) VALUES (?, ?, ?, ?, ?, ?)";
        PreparedStatement stmt = conn.prepareStatement(sql);
        stmt.setString(1, FieldNim.getText());
        stmt.setString(2, FieldNama.getText());
        stmt.setString(3, FieldKelas.getText());
        stmt.setString(4, ComboProdi.getSelectedItem().toString());
        stmt.setString(5, FieldUsername.getText());
        stmt.setString(6, FieldPassword.getText());
        stmt.executeUpdate();

        JOptionPane.showMessageDialog(this, "Data berhasil ditambahkan.");
        TampilData();
    } catch (SQLException e) {
        e.printStackTrace();
        JOptionPane.showMessageDialog(this, "Terjadi kesalahan: " + e.getMessage());
    }
}
```

---

## 🔸 Mengubah Data

```java
private void BtnUpdateActionPerformed(java.awt.event.ActionEvent evt) {
    try (Connection conn = DBConnection.getConnection()) {
        String sql = "UPDATE users SET nama=?, kelas=?, jurusan=?, username=?, password=? WHERE nim=?";
        PreparedStatement stmt = conn.prepareStatement(sql);
        stmt.setString(1, FieldNama.getText());
        stmt.setString(2, FieldKelas.getText());
        stmt.setString(3, ComboProdi.getSelectedItem().toString());
        stmt.setString(4, FieldUsername.getText());
        stmt.setString(5, FieldPassword.getText());
        stmt.setString(6, FieldNim.getText());

        int affectedRows = stmt.executeUpdate();

        if (affectedRows > 0) {
            JOptionPane.showMessageDialog(this, "Data berhasil diubah.");
            TampilData();
        } else {
            JOptionPane.showMessageDialog(this, "Data tidak ditemukan atau tidak ada perubahan.");
        }
    } catch (SQLException e) {
        e.printStackTrace();
        JOptionPane.showMessageDialog(this, "Terjadi kesalahan: " + e.getMessage());
    }
}
```

---

## 🔸 Menghapus Data

```java
private void BtnHapusActionPerformed(java.awt.event.ActionEvent evt) {
    try {
        int selectedRow = LoadTable.getSelectedRow();
        if (selectedRow == -1) {
            JOptionPane.showMessageDialog(this, "Pilih data yang ingin dihapus!");
            return;
        }

        String nim = LoadTable.getValueAt(selectedRow, 0).toString();
        int confirm = JOptionPane.showConfirmDialog(this, "Yakin ingin menghapus data?", "Konfirmasi", JOptionPane.YES_NO_OPTION);
        if (confirm == JOptionPane.YES_OPTION) {
            Connection conn = DBConnection.getConnection();
            String sql = "DELETE FROM users WHERE nim=?";
            PreparedStatement stmt = conn.prepareStatement(sql);
            stmt.setString(1, nim);
            stmt.executeUpdate();
            JOptionPane.showMessageDialog(this, "Data berhasil dihapus!");
            TampilData();
        }
    } catch (SQLException e) {
        e.printStackTrace();
        JOptionPane.showMessageDialog(this, "Gagal menghapus data!");
    }
}
```

---

## 🔸 Mencetak Data ke Console / Pop-up

```java
private void BtnCetakActionPerformed(java.awt.event.ActionEvent evt) {
    DefaultTableModel model = (DefaultTableModel) LoadTable.getModel();
    StringBuilder sb = new StringBuilder();

    for (int i = 0; i < model.getRowCount(); i++) {
        sb.append("No: ").append(i + 1)
            .append(", NIM: ").append(model.getValueAt(i, 1))
            .append(", Nama: ").append(model.getValueAt(i, 2))
            .append(", Kelas: ").append(model.getValueAt(i, 3))
            .append(", Jurusan: ").append(model.getValueAt(i, 4))
            .append(", Username: ").append(model.getValueAt(i, 5))
            .append(", Password: ").append(model.getValueAt(i, 6))
            .append("\n");
    }

    System.out.println(sb.toString());
    JOptionPane.showMessageDialog(this, sb.toString(), "Data Mahasiswa", JOptionPane.INFORMATION_MESSAGE);
}
```

---

## 🔸 Menampilkan Data ke Form Saat Baris di Klik

```java
private void LoadTableMouseClicked(java.awt.event.MouseEvent evt) {
    int baris = LoadTable.getSelectedRow();

    FieldNim.setText(model.getValueAt(baris, 0).toString());
    FieldNama.setText(model.getValueAt(baris, 1).toString());
    FieldKelas.setText(model.getValueAt(baris, 2).toString());
    ComboProdi.setSelectedItem(model.getValueAt(baris, 3).toString());
    FieldUsername.setText(model.getValueAt(baris, 4).toString());
    FieldPassword.setText(model.getValueAt(baris, 5).toString());
}
```

---

## 📝 Catatan

- Komponen seperti `FieldNim`, `ComboProdi`, dan `LoadTable` adalah komponen GUI (`JTextField`, `JComboBox`, `JTable`).
- Pastikan `DBConnection.getConnection()` mengembalikan koneksi valid ke MySQL.
- Untuk keamanan, sebaiknya password tidak ditampilkan di tabel/GUI pada aplikasi asli.
