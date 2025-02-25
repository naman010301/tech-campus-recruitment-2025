#include <iostream>
#include <fstream>
#include <string>
#include <filesystem>

using namespace std;
namespace fs = filesystem;

void extractLogs(const string& inputFile, const string& date) {
    ifstream logFile(inputFile);
    if (!logFile) {
        cerr << "Error opening file: " << inputFile << endl;
        return;
    }

    string outputDir = "output/";
    fs::create_directories(outputDir);
    string outputFile = outputDir + "filtered_logs_" + date + ".txt";
    ofstream outFile(outputFile);

    if (!outFile) {
        cerr << "Error creating file: " << outputFile << endl;
        return;
    }

    string entry;
    while (getline(logFile, entry)) {
        if (entry.compare(0, 10, date) == 0) {
            outFile << entry << endl;
        }
    }

    cout << "Logs for " << date << " saved in: " << outputFile << endl;

    logFile.close();
    outFile.close();
}

bool validateDateFormat(const string& date) {
    return date.size() == 10 && date[4] == '-' && date[7] == '-' && isdigit(date[0]);
}

int main(int argc, char* argv[]) {
    if (argc != 3) {
        cerr << "Usage: " << argv[0] << " <log_file> <YYYY-MM-DD>" << endl;
        return 1;
    }

    string logFile = argv[1];
    string date = argv[2];

    if (!validateDateFormat(date)) {
        cerr << "Invalid date format. Use YYYY-MM-DD." << endl;
        return 1;
    }

    extractLogs(logFile, date);
    return 0;
}
