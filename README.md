package com.test.test;

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
import java.util.List;

class JobData {

	String orderID;
	String shipName;
	String shipCountry;
	String shipCity;
	String shipAddress;

	public JobData(String orderID, String shipName, String shipCountry, String shipCity, String shipAddress) {
		this.orderID = orderID;
		this.shipName = shipName;
		this.shipCountry = shipCountry;
		this.shipCity = shipCity;
		this.shipAddress = shipAddress;
	}

}

public class App {

	WebDriver driver;
	static String filePathString = "C:\\Jobdata\\JobStatus.xlsx";
	private static String[] columns = { "orderID", "shipName", "shipName", "shipCity", "shipAddress" };
	public static List<JobData> jobList1 = new ArrayList<JobData>();

	public void initMethod() {
		System.setProperty("webdriver.chrome.driver",
				"C:\\Users\\MAHESH\\Downloads\\chromedriver_win32\\chromedriver.exe");
		driver = new ChromeDriver();

		String baseUrl = "https://demos.telerik.com/kendo-ui/grid/frozen-columns";

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

		String tbl1 = "//div[@class='k-grid-content-locked']/table/tbody";
		String tbl2 = "//div[@class='k-grid-content k-auto-scrollable']/table/tbody";

		String cntSpan = driver.findElement(By.xpath("//span[@class='k-pager-info k-label']")).getText();
		int cnt = Integer.parseInt(cntSpan.split(" ")[4]);
		cnt = cnt / 30;

		WebElement lnkNext = driver.findElement(By.xpath("//a[@title='Go to the next page']"));
		System.out.println("Size " + cnt);
		// span[@class='k-pager-info k-label']

		for (int m = 1; m <= 5; m++) {
			System.out.println("RowSize" + tableRow.size());
			for (int i = 1; i < tableRow.size(); i++) {
				String str1, str2, str3, str4, str5;
				for (int j = 1; j < tableCols.size(); j++) {

					WebElement cell1 = driver.findElement(By.xpath(tbl1 + "/tr[" + i + "]/td[" + j + "]"));
					new Actions(driver).moveToElement(cell1).perform();
					System.out.println(cell1.getText());
					str1 = cell1.getText();

					WebElement cell2 = driver.findElement(By.xpath(tbl1 + "/tr[" + i + "]/td[" + j + "]"));
					new Actions(driver).moveToElement(cell2).perform();
					System.out.println(cell2.getText());
					str2 = cell2.getText();

					WebElement cell3 = driver.findElement(By.xpath(tbl2 + "/tr[" + i + "]/td[" + j + "]"));
					new Actions(driver).moveToElement(cell3).perform();
					System.out.println(cell3.getText());
					str3 = cell3.getText();

					WebElement cell4 = driver.findElement(By.xpath(tbl2 + "/tr[" + i + "]/td[" + (j + 1) + "]"));
					new Actions(driver).moveToElement(cell4).perform();
					System.out.println(cell4.getText());
					str4 = cell4.getText();

					WebElement cell5 = driver.findElement(By.xpath(tbl2 + "/tr[" + i + "]/td[" + (j + 2) + "]"));
					new Actions(driver).moveToElement(cell5).perform();
					System.out.println(cell5.getText());
					str5 = cell5.getText();

					// jobList1.add(str1, str2, str3, str4, str5);
				}

			}
			lnkNext.click();
		}

		processExcel(List < JobData > jobList1);
	}

	public static void processExcel(List<JobData> jobList) throws IOException {

		if (Files.exists(Paths.get(filePathString))) {

		}

		else {

			System.out.println(".......here");
			Workbook workbook = new XSSFWorkbook(); // new HSSFWorkbook() for generating `.xls` file
			CreationHelper createHelper = workbook.getCreationHelper();
			Sheet sheet = workbook.createSheet("Job");
			Font headerFont = workbook.createFont();
			headerFont.setBold(true);
			headerFont.setFontHeightInPoints((short) 14);
			headerFont.setColor(IndexedColors.RED.getIndex());
			CellStyle headerCellStyle = workbook.createCellStyle();

			// Create Other rows and cells with employees data
			int rowNum = sheet.getLastRowNum();
			for (JobData job : jobList) {
				Row row = sheet.createRow(rowNum++);

				row.createCell(0).setCellValue(job.orderID);
				row.createCell(0).setCellValue(job.shipName);
				row.createCell(0).setCellValue(job.shipCountry);
				row.createCell(0).setCellValue(job.shipCity);
				row.createCell(0).setCellValue(job.shipAddress);

			}

			headerCellStyle.setFont(headerFont);
			Row headerRow = sheet.createRow(0);
			for (int i = 0; i < columns.length; i++) {
				Cell cell = headerRow.createCell(i);
				cell.setCellValue(columns[i]);
				cell.setCellStyle(headerCellStyle);
			}
			CellStyle dateCellStyle = workbook.createCellStyle();
			dateCellStyle.setDataFormat(createHelper.createDataFormat().getFormat("dd-MM-yyyy"));
			// Resize all columns to fit the content size
			for (int i = 0; i < columns.length; i++) {
				sheet.autoSizeColumn(i);
			}

			FileOutputStream fileOut = new FileOutputStream(filePathString);
			workbook.write(fileOut);
			fileOut.close();
			workbook.close();

		}

	}

	//

	public static void main(String[] args) throws IOException {
		App obj = new App();

		obj.initMethod();
		obj.ReadCells();
		System.out.println("Hello World!");
	}
}
