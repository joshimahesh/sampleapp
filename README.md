package com.testdemo.testDemo;

import java.nio.file.Files;
import java.nio.file.Paths;
import java.util.List;
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.interactions.Actions;
import org.apache.poi.openxml4j.exceptions.InvalidFormatException;
import org.apache.poi.ss.usermodel.*;
import org.apache.poi.xssf.usermodel.XSSFWorkbook;
import java.io.FileOutputStream;
import java.io.IOException;
import java.util.ArrayList;
import java.util.Calendar;
import java.util.Date;

class JobData {

	String orderID;
	String shipName;
	String shipCountry;
	String shipCity;
	String shipAddress;

	public JobData(String orderID) {
		this.orderID = orderID;
	}

	public void setshipName(String shipName) {
		this.shipName = shipName;
	}

	public void setshipCountry(String shipCountry) {
		this.shipCountry = shipCountry;
	}

	public void setshipCity(String shipCity) {
		this.shipCity = shipCity;
	}

	public void setshipAddress(String shipAddress) {
		this.shipAddress = shipAddress;
	}
}

public class App {
	static WebDriver driver;
	static String filePathString = "C:\\Jobdata\\JobStatus.xlsx";
	private static String[] columns = { "orderID", "shipName", "shipName", "shipCity", "shipAddress" };
	public static List<JobData> jobList1 = new ArrayList<JobData>();

	public void initMethod() {
		System.setProperty("webdriver.chrome.driver", "C:\\drivers\\chromedriver.exe");
		driver = new ChromeDriver();

		String baseUrl = "file:///C:/Users/maheshj.BITWISEGLOBAL/Downloads/kendoui.for.jquery.2018.3.1017.trial/examples/grid/frozen-columns.html";

		// launch Fire fox and direct it to the Base URL
		driver.get(baseUrl);
		driver.manage().window().maximize();

	}

	public void ReadCells() throws IOException {

		// To find third row of table
		List<WebElement> tableRow = driver
				.findElements(By.xpath("//div[@class=\"k-grid-content-locked\"]/table/tbody/tr"));

		List<WebElement> tableCols = driver
				.findElements(By.xpath("//div[@class=\"k-grid-content-locked\"]/table/tbody/tr[1]/td"));

		List<WebElement> tableColsNext = driver
				.findElements(By.xpath("//div[@class='k-grid-content k-auto-scrollable']/table/tbody/tr[1]/td"));

		String tbl1 = "//div[@class='k-grid-content-locked']/table/tbody";
		String tbl2 = "//div[@class='k-grid-content k-auto-scrollable']/table/tbody";

		String cntSpan = driver.findElement(By.xpath("//span[@class='k-pager-info k-label']")).getText();
		int cnt = Integer.parseInt(cntSpan.split(" ")[4]);
		cnt = cnt / 50;

		WebElement lnkNext = driver.findElement(By.xpath("//a[@title='Go to the next page']"));
		System.out.println("Size " + cnt);
		// span[@class='k-pager-info k-label']

		ArrayList<JobData> arraylist = new ArrayList<JobData>();
		boolean isFirstTableDoene = false;
		boolean isSecondTableDoene = false;
		for (int m = 1; m <= cnt; m++) {
			/*System.out.println("RowSize" + tableRow.size());
			System.out.println("ColSize" + tableCols.size());
*/
			if (!isFirstTableDoene) {
				isFirstTableDoene = true;
				for (int j = 1; j <= tableCols.size(); j++) {

					for (int i = 1; i <= tableRow.size(); i++) {
						WebElement cell = driver.findElement(By.xpath(tbl1 + "/tr[" + i + "]/td[" + j + "]"));
						if (j == 1) {
							arraylist.add(new JobData(cell.getText()));
						} else if (j == 2) {
							arraylist.get(i - 1).setshipName(cell.getText());
						}
					}
				}
			}

			if (!isSecondTableDoene) {
				isSecondTableDoene = true;
				for (int j = 1; j <= tableColsNext.size(); j++) {

					for (int i = 1; i <= tableRow.size(); i++) {

						WebElement cell = driver.findElement(By.xpath(tbl2 + "/tr[" + i + "]/td[" + j + "]"));
						if (j == 1) {
							arraylist.get(i - 1).setshipCountry(cell.getText());
						}
						if (j == 2) {
							arraylist.get(i - 1).setshipCity(cell.getText());
						}
						if (j == 3) {
							arraylist.get(i - 1).setshipAddress(cell.getText());
						}
					}
				}
			}
			
			System.out.println("---LIST--" + arraylist.size());
			for (JobData jobData : arraylist) {
				System.out.println(jobData.orderID + " | " + jobData.shipName + " | " + jobData.shipCountry + " | "
						+ jobData.shipCity + " | " + jobData.shipAddress);

			}
			
			arraylist.clear();

			isFirstTableDoene = isSecondTableDoene = false;
			lnkNext.click();
		}

		

	}

	public static void main(String[] args) throws IOException {
		App obj = new App();
		obj.initMethod();
		obj.ReadCells();
		driver.quit();
	}

}
