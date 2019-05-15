#### Stock.java
```java
package jp.shop.dto;

import java.sql.Timestamp;

public class Stock {

    private int id;
    private String item;
    private int price;
    private int quantity;
    private Timestamp updateDate;

    public int getId() {
        return id;
    }
    public void setId(int id) {
        this.id = id;
    }
    public String getItem() {
        return item;
    }
    public void setItem(String item) {
        this.item = item;
    }
    public int getPrice() {
        return price;
    }
    public void setPrice(int price) {
        this.price = price;
    }
    public int getQuantity() {
        return quantity;
    }
    public void setQuantity(int quantity) {
        this.quantity = quantity;
    }
    public Timestamp getUpdateDate() {
        return updateDate;
    }
    public void setUpdateDate(Timestamp updateDate) {
        this.updateDate = updateDate;
    }

}
```

#### StockDAO.java
```java
package jp.shop.dao;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.ArrayList;
import java.util.List;

import jp.shop.dto.Stock;

public class StockDAO {

    private final String URL = "jdbc:mysql://localhost:3306/sample_shop?useSSL=false";
    private final String USER = "root";
    private final String PASSWORD = "";   // TODO DBのパスワード

    /**
     * findAllメソッド
     * @return List<Stock> 在庫情報
     */
    public List<Stock> findAll() {
        List<Stock> listItem = new ArrayList<>();
        String sql = "SELECT * FROM stock";

        try (Connection con = DriverManager.getConnection(URL, USER, PASSWORD);
             PreparedStatement ps = con.prepareStatement(sql)) {

            ResultSet rs = ps.executeQuery();

            while (rs.next()) {
                Stock item = new Stock();
                item.setId(rs.getInt("id"));
                item.setItem(rs.getString("item"));
                item.setPrice(rs.getInt("price"));
                item.setQuantity(rs.getInt("quantity"));
                item.setUpdateDate(rs.getTimestamp("update_date"));
                listItem.add(item);
            }

        } catch (SQLException e) {
            e.printStackTrace();
        }

        return listItem;
    }

    /**
     * insertメソッド
     * @param stock 在庫情報
     * @return int 登録件数
     */
    public int insert(Stock stock) {
        int result = 0;
        String sql = "INSERT INTO stock (item, price, quantity) VALUES (?, ?, ?)";

        try (Connection con = DriverManager.getConnection(URL, USER, PASSWORD);
             PreparedStatement ps = con.prepareStatement(sql)) {

            ps.setString(1, stock.getItem());
            ps.setInt(2, stock.getPrice());
            ps.setInt(3, stock.getQuantity());

            result = ps.executeUpdate();

        } catch (SQLException e) {
            e.printStackTrace();
        }

        return result;
    }

    /**
     * updateメソッド
     * @param stock 在庫情報
     * @return int 更新件数
     */
    public int update(Stock stock) {
        int result = 0;
        String sql = "UPDATE stock SET item = ?, price = ?, quantity = ? WHERE id = ?";

        try (Connection con = DriverManager.getConnection(URL, USER, PASSWORD);
             PreparedStatement ps = con.prepareStatement(sql)) {

            ps.setString(1, stock.getItem());
            ps.setInt(2, stock.getPrice());
            ps.setInt(3, stock.getQuantity());
            ps.setInt(4, stock.getId());

            result = ps.executeUpdate();

        } catch (SQLException e) {
            e.printStackTrace();
        }

        return result;
    }

    /**
     * deleteメソッド
     * @param id 更新対象ID
     * @return int 削除件数
     */
    public int delete(int id) {
        int result = 0;
        String sql = "DELETE FROM stock WHERE id = ?";

        try (Connection con = DriverManager.getConnection(URL, USER, PASSWORD);
             PreparedStatement ps = con.prepareStatement(sql)) {

            ps.setInt(1, id);
            result = ps.executeUpdate();

        } catch (SQLException e) {
            e.printStackTrace();
        }

        return result;
    }

}
```

#### StockMain.java
```java
package jp.shop.main;

import java.util.List;
import java.util.Scanner;

import jp.shop.dao.StockDAO;
import jp.shop.dto.Stock;

public class StockMain {

    private static StockDAO dao = new StockDAO();
    private static Scanner scan = new Scanner(System.in);

    /**
     * 在庫登録処理
     */
    static void insert() {
        System.out.println();
        System.out.println("【在庫登録】");
        System.out.println("------ 登録情報を入力してください ------");

        System.out.print("商品名：");
        String item = scan.next();
        System.out.print("価格：");
        int price = scan.nextInt();
        System.out.print("数量：");
        int quantity = scan.nextInt();

        // TODO Stockクラスのインスタンスを生成し、登録内容をセットする
        Stock stock = new Stock();
        stock.setItem(item);
        stock.setPrice(price);
        stock.setQuantity(quantity);

        /*
         * TODO
         * StockDAOのinsertメソッドを実行する
         * 成功時：「登録が完了しました。」
         * 失敗時：「登録に失敗しました。」
         */
        if (dao.insert(stock) > 0) {
            System.out.println("\r\n登録が完了しました。");
        } else {
            System.out.println("\r\n登録に失敗しました。");
        }

        System.out.println("----------------------------------------");
        System.out.println();

    }

    /**
     * 在庫更新処理
     */
    static void update() {
        System.out.println();
        System.out.println("【在庫更新】");
        System.out.println("------ 更新するIDを入力してください ------");

        System.out.print("ID：");
        int id = scan.nextInt();

        System.out.println();
        System.out.println("------ 更新内容を入力してください --------");
        System.out.print("商品名：");
        String item = scan.next();
        System.out.print("価格：");
        int price = scan.nextInt();
        System.out.print("数量：");
        int quantity = scan.nextInt();

        // TODO Stockクラスのインスタンスを生成し、更新内容をセットする
        Stock stock = new Stock();
        stock.setId(id);
        stock.setItem(item);
        stock.setPrice(price);
        stock.setQuantity(quantity);

        /*
         * TODO
         * StockDAOのupdateメソッドを実行する
         * 成功時：「更新が完了しました。」
         * 失敗時：「更新に失敗しました。」
         */
        if (dao.update(stock) > 0) {
            System.out.println("\r\n更新が完了しました。");
        } else {
            System.out.println("\r\n更新に失敗しました。");
        }

        System.out.println("------------------------------------------");
        System.out.println();

    }

    /**
     * 在庫削除処理
     */
    static void delete() {
        System.out.println();
        System.out.println("【在庫削除】");
        System.out.println("------ 削除するIDを入力してください ------");

        System.out.print("ID：");
        int id = scan.nextInt();

        /*
         * TODO
         * StockDAOのupdateメソッドを実行する
         * 成功時：「削除が完了しました。」
         * 失敗時：「削除に失敗しました。」
         */
        if (dao.delete(id) > 0) {
            System.out.println("\r\n削除が完了しました。");
        } else {
            System.out.println("\r\n削除に失敗しました。");
        }

        System.out.println("------------------------------------------");
        System.out.println();

    }

    public static void main(String[] args) {

        while (true) {
            List<Stock> listItem = dao.findAll();

            System.out.println("【在庫一覧】");
            System.out.println(String.format("%-5s", "ID") + String.format("%-20s", "ITEM") + String.format("%-10s", "PRICE") + "QUANTITY");
            System.out.println("--------------------------------------------------");
            for (Stock item : listItem) {
                System.out.println(String.format("%-5s", item.getId()) + String.format("%-20s", item.getItem()) +
                                   String.format("%-10s", item.getPrice()) + item.getQuantity());
            }

            System.out.println("\r\n*** 処理番号を選択 ***");
            System.out.println("1:登録　2:更新　3:削除　4:終了");
            int select = scan.nextInt();

            if (select == 1) {
                insert();
            } else if (select == 2) {
                update();
            } else if (select == 3) {
                delete();
            } else {
                System.out.println("\r\n処理を終了します。");
                break;
            }
        }
    }
}

```