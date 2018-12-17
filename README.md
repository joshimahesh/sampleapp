package com.test.test;

import java.io.FileOutputStream;
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Paths;
import java.text.SimpleDateFormat;
import java.util.ArrayList;
import java.util.Date;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.concurrent.Callable;

import org.apache.poi.ss.usermodel.Cell;
import org.apache.poi.ss.usermodel.CellStyle;
import org.apache.poi.ss.usermodel.CreationHelper;
import org.apache.poi.ss.usermodel.Font;
import org.apache.poi.ss.usermodel.IndexedColors;
import org.apache.poi.ss.usermodel.Row;
import org.apache.poi.ss.usermodel.Sheet;
import org.apache.poi.ss.usermodel.Workbook;
import org.apache.poi.xssf.usermodel.XSSFWorkbook;
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;

class JobData1 {

	String orderID;
	String shipName;
	String shipCountry;
	String shipCity;
	String shipAddress;

	public JobData1(String orderID) {
		this.orderID = orderID;
	}

	public void shipName(String shipName) {
		this.shipName = shipName;
	}

	public void shipCountry(String shipCountry) {
		this.shipCountry = shipCountry;
	}

	public void shipCity(String shipCity) {
		this.shipCity = shipCity;
	}

	public void shipAddress(String shipAddress) {
		this.shipAddress = shipAddress;
	}

}

public class TestClass {

	public static WebDriver driver;
	static ArrayList<JobData1> list = new ArrayList<JobData1>();

	private static String[] columns = { "orderID", "shipName", "shipName", "shipCity", "shipAddress" };

	public TestClass() {
		System.setProperty("webdriver.chrome.driver",
				"C:\\Users\\MAHESH\\Downloads\\chromedriver_win32\\chromedriver.exe");
		driver = new ChromeDriver();
		driver.get(
				"file:///C:/Users/MAHESH/Downloads/kendoui.for.jquery.2018.3.1017.trial/examples/grid/frozen-columns.html");
	}

	public void performOperation() throws IOException {

		List<WebElement> tableOneCol = driver.findElements(By.xpath("//*[@class='k-grid-content-locked']//tr[1]//td"));
		List<WebElement> tableTwoCol = driver
				.findElements(By.xpath("//*[@class='k-grid-content k-auto-scrollable']//tr[1]//td"));

		for (int i = 1; i <= tableOneCol.size(); i++) {
			List<WebElement> tableRow1 = driver
					.findElements(By.xpath("//*[@class='k-grid-content-locked']//tr//td[" + i + "][text() !='']"));
			CreateList(tableRow1, i);
		}

		for (int j = 1; j <= tableTwoCol.size(); j++) {
			List<WebElement> tableRow1 = driver.findElements(
					By.xpath("//*[@class='k-grid-content k-auto-scrollable']//tr//td[" + j + "][text() !='']"));
			CreateList(tableRow1, j + 2);
		}

		for (JobData1 job : list) {
			System.out.println(job.orderID + "|" + job.shipName + "|" + job.shipCountry + "|" + job.shipCity + "|"
					+ job.shipAddress);
		}

		processExcel(list);

	}

	public static void CreateList(List<WebElement> tableRowParam, int iVal) {
		if (iVal == 1) {
			for (int iValParam = 0; iValParam < tableRowParam.size(); iValParam++) {
				JobData1 o = new JobData1(tableRowParam.get(iValParam).getText());
				list.add(o);
			}
		}

		else if (iVal == 2) {
			for (int iValParam = 0; iValParam < tableRowParam.size(); iValParam++) {
				list.get(iValParam).shipName(tableRowParam.get(iValParam).getText());
			}
		}

		else if (iVal == 3) {
			for (int iValParam = 0; iValParam < tableRowParam.size(); iValParam++) {
				list.get(iValParam).shipCountry(tableRowParam.get(iValParam).getText());
			}
		} else if (iVal == 4) {
			for (int iValParam = 0; iValParam < tableRowParam.size(); iValParam++) {
				list.get(iValParam).shipCity(tableRowParam.get(iValParam).getText());
			}
		} else if (iVal == 5) {
			for (int iValParam = 0; iValParam < tableRowParam.size(); iValParam++) {
				list.get(iValParam).shipAddress(tableRowParam.get(iValParam).getText());
			}
		}
	}

	public static void processExcel(List<JobData1> jobList) throws IOException {
		String filePathString = "C:\\Jobdata\\JobStatus";

		String pattern = "yyyyMMdd";
		SimpleDateFormat simpleDateFormat = new SimpleDateFormat(pattern);

		String date = simpleDateFormat.format(new Date());
		filePathString = filePathString + date + ".xlsx";

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
			for (JobData1 job : jobList) {
				Row row = sheet.createRow(rowNum++);

				row.createCell(0).setCellValue(job.orderID);
				row.createCell(1).setCellValue(job.shipName);
				row.createCell(2).setCellValue(job.shipCountry);
				row.createCell(3).setCellValue(job.shipCity);
				row.createCell(4).setCellValue(job.shipAddress);

			}

			
			Row row = sheet.createRow(rowNum++);
			for (int i = 0; i < columns.length; i++) {
				Cell cell1 = row.createCell(i);
				cell1.setCellValue(columns[i]);
				cell1.setCellStyle(headerCellStyle);

				Cell cell2 = row.createCell(i + 1);
				cell2.setCellValue(columns[i + 1]);
				cell2.setCellStyle(headerCellStyle);

				Cell cell3 = row.createCell(i + 2);
				cell3.setCellValue(columns[i + 2]);
				cell3.setCellStyle(headerCellStyle);

				Cell cell4 = row.createCell(i + 3);
				cell4.setCellValue(columns[i + 3]);
				cell4.setCellStyle(headerCellStyle);

				Cell cell5 = row.createCell(i + 4);
				cell5.setCellValue(columns[i + 4]);
				cell5.setCellStyle(headerCellStyle);
				
				
			}
			CellStyle dateCellStyle = workbook.createCellStyle();
			
			
		
	        
	        
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

	public static void main(String[] args) throws IOException {

		new TestClass().performOperation();
		driver.quit();
	}

}
