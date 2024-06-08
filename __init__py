#!/usr/bin/python3

""" Print status for PR-500MI Home router.
"""

import argparse
import bs4
import re
import requests
import socket
import urllib.parse
import yaml

class getInfo(object):
    def __init__(self, user="", passwd="", url="http://192.168.1.1"):
        """ init """
        self.rs = requests.Session()
        self.url = url
        self.user = user
        self.passwd= passwd

    def get_all_data(self, yamldump=None):
        """ get information """
        all_data = {}
        all_data["telephone_histories"] = self.get_telephone_histories()
        all_data["static_route_4"] = self.get_static_route_4()
        all_data["ip_masq_4"] = self.get_ip_masq_4()
        all_data["current_status"] = self.get_current_status()
        if yamldump:
            return yaml.dump(all_data, allow_unicode=True)
        return all_data

    def get_telephone_histories(self):
        """ get histories """
        _bs = bs4.BeautifulSoup(
            self.rs.get(
                f"{self.url}/ntt/information/callHistory",
                auth=requests.auth.HTTPBasicAuth(self.user, self.passwd)).text,
            "html.parser")
        _txt = _bs.select("body div pre")[0].contents[0]
        _l = re.findall(r"^\s*([0-9]+)\.\s(-|[0-9/]+\s+[0-9:]+)\s+(-|[0-9/]+\s+[0-9:]+)\s+(-|[0-9/]+\s+[0-9:]+)\s+(.+)$", _txt, re.M)
        _l = [list(_[:4]) + _[4].split()[1:-4] for _ in _l]
        return _l

    def get_static_route_4(self):
        """ get static routing """
        _sr = []
        _bs = bs4.BeautifulSoup(
            self.rs.get(
                f"{self.url}/ntt/detail/staticRouting",
                auth=requests.auth.HTTPBasicAuth(self.user, self.passwd)).text,
            "html.parser")
        _tb = _bs.select("body form#SET_STATICROUTING_SETTING_FORM table tbody")[0]
        for _tr in _tb:
            _tds = _tr.select("td")
            _sr.append(
                {
                    "name": _tds[0].input["name"],
                    "number": int(_tds[1].text),
                    "enabled": not _tds[0].input.has_attr("disabled"),
                    "prefix": _tds[2].text,
                    "gateway": _tds[3].text,
                })
        return _sr

    def get_ip_masq_4(self):
        """ get ipv4 masquerade settings """
        _mq = []
        _bs = bs4.BeautifulSoup(
            self.rs.get(
                f"{self.url}/ntt/detail/ipMasquerade",
                auth=requests.auth.HTTPBasicAuth(self.user, self.passwd)).text,
            "html.parser")
        _tb = _bs.select("body form#SET_IPMASQUERADE_SETTING_FORM table tbody")[0]
        for _tr in _tb:
            _tds = _tr.select("td")
            if _tds[3].text:
                if re.search(r'^[0-9]', _tds[3].text):
                    _port = int(_tds[3].text)
                else:
                    _port = socket.getservbyname(_tds[3].text)
                _port_str = _tds[3].text
            else:
                _port = ""
                _port_str = ""

            if _tds[5].text:
                if re.search(r'^[0-9]', _tds[5].text):
                    _fwd_pt = int(_tds[5].text)
                else:
                    _fwd_pt = socket.getservbyname(_tds[5].text)
                _fwd_pt_str = _tds[5].text
            else:
                _fwd_pt = ""
                _fwd_pt_str = ""

            _mq.append(
                {
                    "name": _tds[0].input["name"],
                    "number": int(_tds[1].text),
                    "enabled": not _tds[0].input.has_attr("disabled"),
                    "protocol": _tds[2].text,
                    "port": _port,
                    "port_str": _port_str,
                    "fwd_ip": _tds[4].text,
                    "fwd_pt": _fwd_pt,
                    "fwd_pt_str": _fwd_pt_str,
                })
        return _mq

    def get_current_status(self):
        """ get status and stats """
        _st = {}
        _bs = bs4.BeautifulSoup(
            self.rs.get(
                f"{self.url}/ntt/information/fifth/current",
                auth=requests.auth.HTTPBasicAuth(self.user, self.passwd)).text,
            "html.parser")
        __trs = _bs.select("body div.section table.setting tbody tr")
        _st["mac_wan"] = __trs[0].td.text.upper()
        _st["mac_lan"] = __trs[1].td.text.upper()
        _st["wan_4_status"] = __trs[28].td.text
        _st["wan_4_ip"] = __trs[29].td.text
        _st["wan_4_peer"] = __trs[30].td.text
        _st["wan_4_dns"] = __trs[31].td.text.split('/')
        _st["wan_6_status"] = __trs[48].td.text
        _st["wan_6_ip"] = __trs[49].td.text.lower()
        _st["wan_6_peer"] = __trs[50].td.text.lower()
        _st["wan_6_dns"] = __trs[51].td.text.lower().split('/')
        _st["wan_through_put"] = __trs[56].td.text
        _st["hw_status"] = __trs[57].td.text
        return _st

if __name__ == '__main__':
    ap = argparse.ArgumentParser(
        description='Get gateway status.',
        add_help = True,
    )

    ap.add_argument(
        '-u', '--user',
        help='Provide username for gateway',
        required=True,
    )
    ap.add_argument(
        '-p', '--password',
        help='Provide password for gateway',
        required=True,
    )
    ap.add_argument(
        '-U', '--URL',
        help='Provide url like "http://192.168.x.y"',
    )
    opts = ap.parse_args()

    if opts.user and opts.password:
        if opts.URL:
            gi = getInfo(opts.user, opts.password, url=opts.URL)
        else:
            gi = getInfo(opts.user, opts.password)
        print(gi.get_all_data(yamldump=True))
