
package stepone;

import java.io.File;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.concurrent.TimeUnit;

import org.apache.poi.ss.usermodel.Row;
import org.apache.poi.ss.usermodel.Sheet;
import org.apache.poi.xssf.usermodel.XSSFWorkbook;
import org.openqa.selenium.By;
import org.openqa.selenium.Keys;

import com.jcraft.jsch.Channel;
import com.jcraft.jsch.ChannelSftp;
import com.jcraft.jsch.JSch;
import com.jcraft.jsch.JSchException;
import com.jcraft.jsch.Session;
import com.jcraft.jsch.SftpException;

public class steptwo extends TestBase {

	public static String FILE_PATH = "C:\\test_java\\jar-files\\sprint-status-sam.xlsx";
	static List<Map<String, String>> list = new ArrayList<Map<String, String>>();
	static Map<String, String> tmp;
	static String AUTOMATE_URL = "http://jira:10002/login.jsp";
	static int STATUS_ROW_NO = 11;
	static int BACKFILE_UPLOAD = 12;
	static ChannelSftp sftpChannel;
	static Session session = null;
	public static String BACKFILE_FOLDER_PATH = "C:\\test_java\\jar-files\\Backfill_files\\";

	public static void readUserDataForStepTwo(String user, Integer autostatus) throws IOException {
		String excelFilePath = FILE_PATH;
		String excelSheetName = "Sprint 3";
		if (excelFilePath != null && !"".equals(excelFilePath.trim()) && excelSheetName != null
				&& !"".equals(excelSheetName.trim())) {
			try {
				/* Open the file input stream. */
				FileInputStream fis = new FileInputStream(excelFilePath.trim());

				/* Get workbook. */
				XSSFWorkbook excelWookBook = new XSSFWorkbook(fis);

				/* Get sheet by name. */
				Sheet copySheet = excelWookBook.getSheet(excelSheetName);

				int fRowNum = copySheet.getFirstRowNum();
				int lRowNum = copySheet.getLastRowNum();
				/*
				 * First row is excel file header, so read data from row next to
				 * it.
				 */
				for (int i = fRowNum + 1; i < lRowNum + 1; i++) {
					/* Only get desired row data. */
					tmp = new HashMap<String, String>();
					int autoStatusInt = (int) (copySheet.getRow(i).getCell(11)).getNumericCellValue();

					String userTemp = (copySheet.getRow(i).getCell(2)).toString().trim().toLowerCase();
					String userTempActual = user.toString().trim().toLowerCase();
					if (userTemp.equals(userTempActual) && autoStatusInt == autostatus) {
						String rowid = Integer.toString(i);
						tmp.put("row_id", rowid);
						tmp.put("summory_table", (copySheet.getRow(i).getCell(1)).toString());
						tmp.put("backfill_files", (copySheet.getRow(i).getCell(BACKFILE_UPLOAD)).toString());
						list.add(tmp);
					}

				}

			} catch (Exception ex) {
				ex.printStackTrace();
			}
		}

	}

	public static void searchAndUpdate(String element, String backfile_str) throws InterruptedException, SftpException {
		driver.findElement(By.name("searchString")).sendKeys(element);
		driver.findElement(By.name("searchString")).sendKeys(Keys.ENTER);
		Thread.sleep(3000);
		connecttoWinScp();
		String[] file_data = backfile_str.split(",");
		for (int str = 0; str < file_data.length; str++) {
			getWinScpfile(file_data[str]);
			Thread.sleep(1000);
		}

		Thread.sleep(3000);
		for (int str1 = 0; str1 < file_data.length; str1++) {
			driver.findElement(By.xpath("//input[@type='file']")).sendKeys(BACKFILE_FOLDER_PATH + file_data[str1]);
		}
		sftpChannel.exit();
		session.disconnect();
		Thread.sleep(4000);
	}

	public static ChannelSftp connecttoWinScp() {
		JSch jsch = new JSch();

		try {
			session = jsch.getSession("username", "host", 22);
			session.setConfig("StrictHostKeyChecking", "no");
			session.setPassword("password");
			session.connect();

			Channel channel = session.openChannel("sftp");
			channel.connect();
			sftpChannel = (ChannelSftp) channel;

		} catch (JSchException e) {
			e.printStackTrace();
		}

		return sftpChannel;
	}

	public static void getWinScpfile(String filename) throws SftpException {
		sftpChannel.get("/var/log/" + filename, BACKFILE_FOLDER_PATH + filename);

	}

	public static void afterSearchUpdateStatusInExcel(String column_id, String summery_table) {
		String excelFilePath = FILE_PATH;
		String excelSheetName = "Sprint 3";
		if (excelFilePath != null && !"".equals(excelFilePath.trim()) && excelSheetName != null
				&& !"".equals(excelSheetName.trim())) {
			try {

				FileInputStream fis = new FileInputStream(excelFilePath.trim());

				XSSFWorkbook excelWookBook = new XSSFWorkbook(fis);
				int updateActualColumn = Integer.parseInt(column_id);
				Sheet copySheet = excelWookBook.getSheet(excelSheetName);
				copySheet.getRow(updateActualColumn).getCell(STATUS_ROW_NO).setCellValue(1);
				FileOutputStream output_file = new FileOutputStream(new File(excelFilePath.trim())); // Open
																										// //
																										// updates
				excelWookBook.write(output_file); // write changes
				output_file.close();

			} catch (Exception ex) {
				ex.printStackTrace();
			}
		}
	}

	public static void main(String[] args) {
		try {
			enterUserfromPrompt();
			readUserDataForStepTwo(username_txt_execle, 1);

			if (list.size() > 0) {
				loginPortal();
				for (Map<String, String> map : list) {
					try {
						searchAndUpdate(map.get("summory_table"), map.get("backfill_files"));
					} catch (SftpException e) {
						// TODO Auto-generated catch block
						e.printStackTrace();
					}
					afterSearchUpdateStatusInExcel(map.get("row_id"), map.get("summory_table"));
				}
			} else {
				System.out.println("ALERT: Hi, We don't have any more records to update. Please check");
			}

		} catch (IOException e) {
			e.printStackTrace();
		} catch (InterruptedException e) {
			e.printStackTrace();
		}

	}

}
-------------------------------------------
package com.test.test;

import java.io.File;
import java.io.FileInputStream;
import java.util.Arrays;

import org.apache.poi.ss.usermodel.CellType;
import org.apache.poi.xssf.usermodel.XSSFCell;
import org.apache.poi.xssf.usermodel.XSSFRow;
import org.apache.poi.xssf.usermodel.XSSFSheet;
import org.apache.poi.xssf.usermodel.XSSFWorkbook;

public class DEMO {

	public void readExcel() throws Exception {
		int numRows = 0, numCols = 0;
		// Calculate array size
		File file = new File("C:\\workspace\\test\\src\\test\\java\\com\\test\\test\\");
		File[] files = file.listFiles();
		for (File f : files) {
			FileInputStream myStream = new FileInputStream(f.getPath());
			XSSFWorkbook myWorkbook = new XSSFWorkbook(myStream);
			XSSFSheet mySheet = myWorkbook.getSheetAt(0);
			numRows = numRows + mySheet.getLastRowNum();
			numCols = mySheet.getRow(0).getLastCellNum();
			System.out.println("P" + numRows);
			
		}
		numRows = numRows + 2;
		String[][] excelData = new String[numRows+2][numCols];
		
		
		System.out.println("Accessing spreadsheet and setting up Array");
		File file1 = new File("C:\\workspace\\test\\src\\test\\java\\com\\test\\test\\");
		File[] files1 = file1.listFiles();
		for (File f1 : files1) {
			FileInputStream myStream = new FileInputStream(f1.getPath());
			XSSFWorkbook myWorkbook = new XSSFWorkbook(myStream);
			XSSFSheet mySheet = myWorkbook.getSheetAt(0);
			
			for (int i = 0; i < numRows; i++) {
				XSSFRow row = mySheet.getRow(i);
				for (int j = 0; j < numCols; j++) {
					XSSFCell cell = row.getCell(j);
					String value = cellToString(cell);
					excelData[i][j] = value;
				}
			}
			System.out.println("Array population complete");
			System.out.println(excelData);
			System.out.println(Arrays.deepToString(excelData));
			
		}

		// Access spreadsheet
		
		System.out.println("Populating Array");
	
	}

	public String cellToString(XSSFCell cell) {

		String result;

		// Formulas can't be evaluated, so throw an Exception if one is encountered
		if (cell.getCellTypeEnum() == CellType.FORMULA) {
			throw new RuntimeException("Cannot process a formula. Please change field to result of formula.");
		}
		// If blanks are ever able to be evaluated by Apache POI, set them to empty
		// string
		else if (cell.getCellTypeEnum() == CellType.BLANK) {
			result = " ";
		}
		// Convert cell contents to String
		else {
			result = String.valueOf(cell);
		}
		return result;
	}

	public static void main(String[] args) throws Exception {
		// TODO Auto-generated method stub
		new DEMO().readExcel();
	}

}
