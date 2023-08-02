import string
from http.server import BaseHTTPRequestHandler, HTTPServer
import json
import utils
import coverage
import time


cov = coverage.Coverage()
cov.start()
class RequestHandler(BaseHTTPRequestHandler):
    count = 0
    def do_GET(self):
        self.count = self.count + 1
        self.send_response(200)
        self.send_header('Content-Type', 'application/json')
        self.end_headers()

        response = {
            'message': 'Hello, World!'
        }

        self.wfile.write(json.dumps(response).encode('utf-8'))
        coverage_data = cov.get_data()
        # file = "coverage"+str(self.count)+".json"
        # cov.report(file)
        # print(coverage_data)
        cov.save()
        coverage_data.write()
        cov.erase()
        # cov.start()


    def do_POST(self):

        self.count = self.count + 1
        content_length = int(self.headers['Content-Length'])
        body = self.rfile.read(content_length)
        data = json.loads(body.decode('utf-8'))

        self.send_response(200)
        self.send_header('Content-Type', 'application/json')
        self.end_headers()

        response = {
            'message': 'Received POST request',
            'data': data
        }
        my_sum = utils.add()
        print(my_sum)
        self.wfile.write(json.dumps(response).encode('utf-8'))

        coverage_data = cov.get_data()
        # file = "coverage" + str(self.count) + ".json"
        # cov.report(file)
        # print(coverage_data)
        coverage_data.write()
        # time.sleep(2)
        # cov.erase()
        # cov2.start()


if __name__ == '__main__':
    server_address = ('', 8080)
    httpd = HTTPServer(server_address, RequestHandler)
    print('Server running at localhost:8080')
    httpd.serve_forever()
